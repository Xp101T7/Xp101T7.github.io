---
title: Container Security Basics - A SANS Workshop addition
date: 2023-05-06 12:00:00 -500
categories: [Containers, Cloud]
tags: [Ubuntu,Docker,SANS,Containers]
---
This is an the add-in and detailed write up and picture demo of a SANS workshop on container security.  

This is a 2 hour hands-on workshop. Is your company adopting containers but you haven’t had a chance to figure out the best way to secure them yet? We’ll get hands-on with containers, learn how to examine them for common mistakes, and then add in some security controls like container image signing, create a Software Bill of Materials, and run vulnerability scans.

Learning Objectives:

1.  Use standard utilities to create a variety of secure and insecure container images.
2.  Perform container image signing.
3.  Create a SBOM and then run vulnerability scans on that SBOM. Examine common gaps in SBOM generation tools.
4.  Manually examine container image manifests, indexes, and layers.
5.  Break out of a misconfigured “container”.

Prereqs:

-   Comfortable with Linux command line tools, variables, and system concepts

System Requirements:

-   Access to a Ubuntu 20.04 x86_64 Virtual Machine
-   Steps will be provided to provision this in AWS

Use the Stack config file attached below:


![[Pasted image 20230509191408.png]]

![[Pasted image 20230509191321.png]]

![[Pasted image 20230509191509.png]]

![[Pasted image 20230509192515.png]]

![[Pasted image 20230509192518.png]]

![[Pasted image 20230509192521.png]]

Connect to SSH client

if no .ssh dir make one

	mkdir .ssh/authorized_keys/
	cp ~/Downloads/private.pem ~/.ssh/authorized_keys/private.pem

Now SSH

	ssh -i "private.pem" ubuntu@<Pulic_instance_ip>

Setup the PreReqs

	if [[ -x "$(which sudo)" ]]; then sudo apt-get update; else su -c 'apt-get update'; fi
	if [[ -x "$(which sudo)" ]]; then sudo apt-get -y install ca-certificates curl sudo jq; else su -c 'apt-get -y install ca-certificates curl sudo jq'; fi


Now run the docker script this is only for dev builds not for prod environments. 

	curl -fsSL https://get.docker.com -o get-docker.sh
	sudo sh get-docker.sh

Can only run docker as root

	docker ps

![[Pasted image 20230509193613.png]]

Add User to Docker Group 

	if [[ "$(whoami)" != "root" ]]; then sudo usermod -aG docker "$(whoami)"; fi

**If you know your password** you can run `exec su -l "$(whoami)"` to refresh your group memberships.

```bash
id | grep docker
exec su -l "$(whoami)"

id | grep docker
```

If no pass exit and reconnect to SSH sesh.

	docker ps

![[Pasted image 20230509193832.png]]

Time to Get Started

---

### Container Terms

**Image**
https://docs.docker.com/engine/reference/commandline/image/

**Container**
https://docs.docker.com/engine/reference/commandline/container/
https://www.docker.com/resources/what-container/

**OCI Artifact**
https://docs.docker.com/docker-hub/oci-artifacts/
https://dlorenc.medium.com/oci-artifacts-explained-8f4a77945c13

---

###  Creation of the Image

Images are bundles and need to be spun up with Dockerfiles.

NO NEED TO RUN JUST AN EXAMPLE

```sql
FROM nginx
WORKDIR /
ENTRYPOINT ["/docker-entrypoint.sh"]
CMD ["nginx", "-g", "daemon off;"]
EXPOSE 80
```

Start from nginx so we are using latest https://hub.docker.com/_/nginx/tags

AND

We are pulling from the defualt registry images from Docker Hub https://docs.docker.com/registry/

```D
docker pull nginx

docker inspect nginx:latest | jq '.[].Config.User'
```

The user is empty or wasnt specified so this is unsafe bc it defualts to root.

Check for user also by running

	docker run nginx:latest whoami

<p class="admonition-title">Note</p>
<p>If you need to quickly scan a <code class="docutils literal notranslate"><span class="pre">Dockerfile</span></code> to find issues, I suggest <a class="reference external" href="https://github.com/hadolint/hadolint">hadolint</a>
based on its out-of-the-box rules, and if you need more customized options I would look at
<a class="reference external" href="https://www.conftest.dev/">conftest</a> from the Open Policy Agent (OPA) project (you can check out an example
<code class="docutils literal notranslate"><span class="pre">Dockerfile</span></code> policy <a class="reference external" href="https://github.com/open-policy-agent/conftest/tree/master/examples/docker">here</a>).</p>

---

### Changing the User

Root is not preferred but can secure https://docs.docker.com/engine/security/userns-remap/.  Though still this shouldnt be defualt.

Create a Dockerfile that defines secure image. We are building on top of the nginx image and inherting properties then adding our changes on top. 

```sql
cat << EOF > Dockerfile
FROM nginx
RUN groupadd --gid 53150 -r notroot && useradd -r -g notroot -s "/bin/bash" --create-home --uid 53150 notroot
USER notroot
EOF
```

Then build and see secure user

```bash
docker buildx build -t example-secure .

docker inspect example-secure | jq -r '.[].Config.User'
```


![[Pasted image 20230509195804.png]]

![[Pasted image 20230509195832.png]]
![[Pasted image 20230509195927.png]]

![[Pasted image 20230509195936.png]]

![[Pasted image 20230509195942.png]]

You can still run container insecurely though by telling it to use root.

	docker run --user 0 example-secure whoami

Policy as Code adds additional layers once things like root are no longer the defualt and enforced by design.

---

### Image Signing

Our secure docker is now called examaple-secure.

You can push images to docker hub by using docker push 

https://docs.docker.com/get-started/04_sharing_app/

	docker login
	docker push 

If we pushed we would need to sign the image first. Beware malicous images that are unsigned. 

We can sig sign the image so others can verify our sig to ensure we had sufficient access to push and we had the private sig key.

The most precise signing is to sign the digest and not tag. Latest tag used earlier can point to other images over time. 

Get the digest from our image.

	docker inspect --format='{{index .RepoDigests 0}}' example-secure || true

Didnt work so we try a few other ways.

	docker inspect --format='{{.RepoDigests}}' example-secure

	docker images --digests example-secure

![[Pasted image 20230509201135.png]]

No Digest here. So we are seeing another docker specific implementation detail. A Digest will not be created for new images until its pushed to a registry(manifest digest, and V2 reg.) or if it was pulled from V2 reg.

	docker inspect --format='{{index .RepoDigests 0}}' nginx

<div class="admonition note">
<p class="admonition-title">Note</p>
<p>The actual SHA-256 digest that you receive may differ from the above. This is because the implicit <code class="docutils literal notranslate"><span class="pre">latest</span></code> tag is
updated over time to point to the latest released image, and is exactly why we sign digests instead of tags.</p>
<p>You are able to get the best of both worlds by combining the two approaches and adding the tag as an “annotation” (which
we will do later).</p>
</div>
We can set a digest to our images by running a V2 registry locally and then pushing the image to it. 

First, setup HTTPS, and pull down a registry image and run the container.

<div class="admonition note">
<p class="admonition-title">Note</p>
<p>If you look closely, you’ll notice the use of a “dummy” container below; this is being used to load files into a volume
and is a well known <a class="reference external" href="https://github.com/moby/moby/issues/25245#issuecomment-365980572">work-around</a> for a feature that
can be voted for <a class="reference external" href="https://github.com/docker/cli/issues/1436">here</a>.</p>
</div>

```bash
newdir=$(mktemp -d)
mkdir -p "${newdir}/certs"
pushd "${newdir}/certs"

docker volume create workshop-certs

docker container create --name dummy -v workshop-certs:/certs registry:2

openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -sha256 -days 60 -nodes -subj "/CN=registry"

docker cp cert.pem dummy:/certs/cert.pem

docker cp key.pem dummy:/certs/key.pem

docker run -d -p 443:443 --name registry -v workshop-certs:/certs -e REGISTRY_HTTP_ADDR=0.0.0.0:443 -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/cert.pem -e REGISTRY_HTTP_TLS_KEY=/certs/key.pem registry:2

popd

```

![[Pasted image 20230509201914.png]]

<div class="dropdown hint admonition toggle" id="toggle-1">
<p class="admonition-title" data-target="toggle-1" data-button="button-toggle-1">Answer
        <button type="button" id="button-toggle-1" class="toggle-button" data-target="toggle-1" data-button="button-toggle-1" data-toggle-hint="" aria-label="Toggle hidden content">
            
<svg xmlns="http://www.w3.org/2000/svg" class="tb-icon toggle-chevron-right" width="44" height="44" viewBox="0 0 24 24" stroke-width="1.5" stroke="#000000" fill="none" stroke-linecap="round" stroke-linejoin="round">
<path stroke="none" d="M0 0h24v24H0z" fill="none"></path>
<polyline points="9 6 15 12 9 18"></polyline>
</svg>
        </button></p>
<p>What we’re seeing is not an image digest, but rather a container ID. This is a unique identifier that <code class="docutils literal notranslate"><span class="pre">docker</span></code> creates
for each container. If you were to run the same command again, you will receive a different identifier, for instance:</p>
<div class="no-copybutton highlight-bash notranslate"><div class="highlight"><pre><span></span>$<span class="w"> </span>docker<span class="w"> </span>run<span class="w"> </span>-d<span class="w"> </span>-p<span class="w"> </span><span class="m">443</span>:443<span class="w"> </span>--name<span class="w"> </span>registry2<span class="w"> </span>registry:2
<span class="hll">892c9c6be865fe31007e6c30984983d0a02f9173501c93e4f801afaef42d5469
</span></pre></div>
</div>
<p>Note that now I received an ID starting with <code class="docutils literal notranslate"><span class="pre">892c9</span></code> whereas previously the same command gave an ID starting with <code class="docutils literal notranslate"><span class="pre">fa830</span></code>. You can see the container IDs for all of your running commands by running <code class="docutils literal notranslate"><span class="pre">docker</span> <span class="pre">ps</span></code> and examining the first column:</p>
<div class="highlight-console notranslate"><div class="highlight"><pre id="codecell15"><span></span><span class="gp">$ </span>docker<span class="w"> </span>ps
<span class="go">CONTAINER ID   IMAGE        COMMAND                  CREATED          STATUS          PORTS                  NAMES</span>
<span class="go">fa830229c72a   registry:2   "/entrypoint.sh /etc…"   15 minutes ago   Up 15 minutes   0.0.0.0:443-&gt;443/tcp   registry</span>
</pre><button class="copybtn o-tooltip--left" data-tooltip="Copy" data-clipboard-target="#codecell15">
      <svg xmlns="http://www.w3.org/2000/svg" class="icon icon-tabler icon-tabler-copy" width="44" height="44" viewBox="0 0 24 24" stroke-width="1.5" stroke="#000000" fill="none" stroke-linecap="round" stroke-linejoin="round">
  <title>Copy to clipboard</title>
  <path stroke="none" d="M0 0h24v24H0z" fill="none"></path>
  <rect x="8" y="8" width="12" height="12" rx="2"></rect>
  <path d="M16 8v-2a2 2 0 0 0 -2 -2h-8a2 2 0 0 0 -2 2v8a2 2 0 0 0 2 2h2"></path>
</svg>
    </button></div>
</div>
<p>However, you’ll notice that the container ID is truncated. This truncation is to simplify the readability of the output.
If you’d like avoid truncation, add the <code class="docutils literal notranslate"><span class="pre">--no-trunc</span></code> option:</p>
<div class="highlight-console notranslate"><div class="highlight"><pre id="codecell16"><span></span><span class="gp">$ </span>docker<span class="w"> </span>ps<span class="w"> </span>--no-trunc
<span class="go">CONTAINER ID                                                       IMAGE        COMMAND</span>
<span class="go">CREATED          STATUS          PORTS                  NAMES</span>
<span class="go">fa830229c72a484fa1b1c18ffc9039712b2561d4aa5c8f7856ed00b3e275ed65   registry:2   "/entrypoint.sh /etc/docker/registry/config.yml"      17 minutes ago   Up 17 minutes   0.0.0.0:443-&gt;443/tcp   registry</span>
</pre><button class="copybtn o-tooltip--left" data-tooltip="Copy" data-clipboard-target="#codecell16">
      <svg xmlns="http://www.w3.org/2000/svg" class="icon icon-tabler icon-tabler-copy" width="44" height="44" viewBox="0 0 24 24" stroke-width="1.5" stroke="#000000" fill="none" stroke-linecap="round" stroke-linejoin="round">
  <title>Copy to clipboard</title>
  <path stroke="none" d="M0 0h24v24H0z" fill="none"></path>
  <rect x="8" y="8" width="12" height="12" rx="2"></rect>
  <path d="M16 8v-2a2 2 0 0 0 -2 -2h-8a2 2 0 0 0 -2 2v8a2 2 0 0 0 2 2h2"></path>
</svg>
    </button></div>
</div>
<p>You’ll notice that the output is significantly longer and harder to read now, but it avoids any truncation.</p>
<p>If you’d like to interact with a running container, you can use this container ID, or the associated name (in my
example, <code class="docutils literal notranslate"><span class="pre">registry</span></code>). For instance, here we use the container ID (which will be different in your case):</p>
<div class="no-copybutton highlight-console notranslate"><div class="highlight"><pre><span></span><span class="gp">$ </span>docker<span class="w"> </span><span class="nb">exec</span><span class="w"> </span>fa8302<span class="w"> </span>ps
<span class="go">PID   USER     TIME  COMMAND</span>
<span class="go">    1 root      0:01 registry serve /etc/docker/registry/config.yml</span>
<span class="go">   16 root      0:00 ps</span>
</pre></div>
</div>
<p>And here we use the name to accomplish the same thing:</p>
<div class="highlight-console notranslate"><div class="highlight"><pre id="codecell17"><span></span><span class="gp">$ </span>docker<span class="w"> </span><span class="nb">exec</span><span class="w"> </span>registry<span class="w"> </span>ps
<span class="go">PID   USER     TIME  COMMAND</span>
<span class="go">    1 root      0:01 registry serve /etc/docker/registry/config.yml</span>
<span class="go">   22 root      0:00 ps</span>
</pre><button class="copybtn o-tooltip--left" data-tooltip="Copy" data-clipboard-target="#codecell17">
      <svg xmlns="http://www.w3.org/2000/svg" class="icon icon-tabler icon-tabler-copy" width="44" height="44" viewBox="0 0 24 24" stroke-width="1.5" stroke="#000000" fill="none" stroke-linecap="round" stroke-linejoin="round">
  <title>Copy to clipboard</title>
  <path stroke="none" d="M0 0h24v24H0z" fill="none"></path>
  <rect x="8" y="8" width="12" height="12" rx="2"></rect>
  <path d="M16 8v-2a2 2 0 0 0 -2 -2h-8a2 2 0 0 0 -2 2v8a2 2 0 0 0 2 2h2"></path>
</svg>
    </button></div>
</div>
<p>But wait, <code class="docutils literal notranslate"><span class="pre">fa8302</span></code> isn’t the full container id, nor is it the one that <code class="docutils literal notranslate"><span class="pre">docker</span> <span class="pre">ps</span></code> showed me earlier, so what gives?</p>
<p>Well, <code class="docutils literal notranslate"><span class="pre">docker</span></code> has access to a full list of container IDs on your system, and so it allows you to provide the minimum
number of characters required to uniquely identify a container, for brevity. How convenient!</p>
</div>




