---
title: Container Security Basics - A SANS Workshop addition
date: 2023-05-09 12:00:00 -500
categories: [Containers, Cloud]
tags: [Ubuntu,Docker,SANS,Containers,SBOM]
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


![Pasted image 20230509191408.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509191408.png)

![Pasted image 20230509191321.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509191321.png)

![Pasted image 20230509191509.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509191509.png)

![Pasted image 20230509192515.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509192515.png)

![Pasted image 20230509192518.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509192518.png)

![Pasted image 20230509192521.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509192521.png)

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

![Pasted image 20230509193613.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509193613.png)

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

![Pasted image 20230509193832.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509193832.png)

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


![Pasted image 20230509195804.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509195804.png)

![Pasted image 20230509195832.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509195832.png)
![Pasted image 20230509195927.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509195927.png)

![Pasted image 20230509195936.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509195936.png)

![Pasted image 20230509195942.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509195942.png)

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

![Pasted image 20230509201135.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509201135.png)

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

![Pasted image 20230509201914.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509201914.png)

![Pasted image 20230509202506.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509202506.png)

![Pasted image 20230509202517.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509202517.png)

![Pasted image 20230509202530.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509202530.png)

![Pasted image 20230509202540.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509202540.png)

![Pasted image 20230509202546.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509202546.png)

Wat is the ![Pasted image 20230509202922.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509202922.png)

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


Once we have the registry local instance setup we can push our images there but first we must re-tag the images to include the registry info of our local registry we are pushing to.

	docker tag example-secure localhost:443/example-secure

now we push the fully qualified image name to the local registry and not the docker hub reg. 

	docker push localhost:443/example-secure

![Pasted image 20230509203720.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509203720.png)

Yay, we have an image digest. 🚀🚀🚀

	docker inspect --format='{{index .RepoDigests 0}}' example-secure

![Pasted image 20230509203745.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509203745.png)

---

## Image Signing

Now we have an image digest we can properly sign the image. We need to use the cosign tool to sign and validate the image. [Open Source Security Foundation](https://openssf.org/) project under the [sigstore](https://www.sigstore.dev/) umbrella.

In order to sign the image, we will:

1.  Pull another docker image that contains `cosign` (are you seeing the pattern yet? 😀)
    
2.  Setup some docker networking so the `cosign` container can reach the `registry` container
    
3.  Use the `cosign` container to generate an encrypted keypair
    
4.  Sign the `example-secure` image using the private key
    
5.  Verify the `example-secure` signature using the public key

Check out docker CLI 
https://docs.docker.com/engine/reference/commandline/cli/

```bash
docker pull cgr.dev/chainguard/cosign

docker network create workshop

docker network connect workshop registry

export COSIGN_PASSWORD='example'

docker run -e COSIGN_PASSWORD -u 0 --network workshop -v "$(pwd):/app" -w /app cgr.dev/chainguard/cosign generate-key-pair

image_digest="$(docker inspect --format='{{index .RepoDigests 0}}' localhost:443/example-secure | cut -f2 -d@ )"

docker run -e COSIGN_PASSWORD -u 0 --network workshop -v "$(pwd):/app" -w /app cgr.dev/chainguard/cosign sign --yes --key cosign.key -a tag=latest registry:443/example-secure@"${image_digest}" --allow-insecure-registry
```

![Pasted image 20230509204734.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509204734.png)

![Pasted image 20230509204747.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509204747.png)

![Pasted image 20230509204756.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509204756.png)

Our image is signed!!!

In the above command, we generated a public and private keypair, signed the manifest digest of the `localhost:443/example-secure` image, and pushed that signature to our local registry to live alongside the image.

We also added an annotation of `tag` with the value of `latest` to describe what we are signing at the moment, which is the `latest` tag. These annotations provide valuable additional context, and it is very common to add annotations such as the `git` commit hash, details about the CI/CD pipeline that built and signed the image, etc.

<div>
<p>Although the private key is encrypted, many secret scanning tools don’t have exclusions for encrypted cosign private
keys and will still flag them as insecure.</p>
<p>For instance, <a class="reference external" href="https://github.com/gitleaks/gitleaks">gitleaks</a> (one of my favourite secret scanning tools) will flag an
encrypted cosign private key, and doesn’t have a straightforward workaround due to the Go programming language’s regex
library’s choice not to support negative lookaheads (see the issue I opened on this
<a class="reference external" href="https://github.com/gitleaks/gitleaks/issues/1034">here</a>). The best approach in most cases, albeit imperfect, is to
exclude the specific <code class="docutils literal notranslate"><span class="pre">.key</span></code> file from your secret scanning tool.</p>
</div>

<div> 
<p>Technically, the above approach is not the most secure way to do this because it has a race condition. It requires that
an image be pushed prior to being signed, and has no way to accommodate signing failures.</p>
<p>However, there is not a great alternative right now. <code class="docutils literal notranslate"><span class="pre">docker</span></code> doesn’t populate the repo digest until it’s <code class="docutils literal notranslate"><span class="pre">docker</span> <span class="pre">push</span></code>ed, and <code class="docutils literal notranslate"><span class="pre">cosign</span></code> also doesn’t support a sign-then-push workflow.</p>
<p>There is an <a class="reference external" href="https://github.com/sigstore/cosign/issues/1905">issue open</a> on the <code class="docutils literal notranslate"><span class="pre">cosign</span></code> project to fix this, feel free
to go subscribe to be notified of activity.</p>
</div>

Verify the signature

```bash
docker run -e COSIGN_PASSWORD -u 0 --network workshop -v "$(pwd):/app" -w /app cgr.dev/chainguard/cosign verify --key cosign.pub registry:443/example-secure@"${image_digest}" --allow-insecure-registry

echo $?
```

![Pasted image 20230509205440.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509205440.png)

![Pasted image 20230509205449.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509205449.png)

<p>If you looked carefully, you may have seen the <code class="docutils literal notranslate"><span class="pre">--allow-insecure-registry</span></code> argument. This is only for our test
environment, where we are using a self-signed certificate for the registry. In production this should <strong>not</strong> ever be
used.</p>

Winning!!!! Yayy!!!!

And in addition, evidence of this signing process was added to a public, software supply chain transparency log called rekor.

	curl https://rekor.sigstore.dev/api/v1/log/entries/24296fb24b8ad77ad9ca41820f93cdbef2264692ced5c142d19e2ba859ab9f2b500d1917afe8ef30


![Pasted image 20230509205620.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509205620.png)

View also in web browser
https://search.sigstore.dev/?logIndex=18624203


<div class="seealso admonition">
<p class="admonition-title">Storing Image Signatures</p>
<p>Today, the way that these signatures are hosted is that they are <code class="docutils literal notranslate"><span class="pre">in-toto</span></code>
<a class="reference external" href="https://github.com/in-toto/attestation">attestations</a> bundled into OCI artifacts, and uploaded to a registry with a
specifically formatted name of <code class="docutils literal notranslate"><span class="pre">sha256-&lt;DIGEST&gt;.sig</span></code> where <code class="docutils literal notranslate"><span class="pre">&lt;DIGEST&gt;</span></code> is the image digest.</p>
<p>This works great because the registry only needs to support OCI artifacts, which most (effectively all) of them do, and
if you are running a container, you are always able to look up the digest of the related image.</p>
<p>Luckily, there is something better in the works here as well. Currently, the OCI
<a class="reference external" href="https://github.com/opencontainers/image-spec">image</a> and
<a class="reference external" href="https://github.com/opencontainers/distribution-spec">distribution</a> specifications have a release candidate for a
version 1.1 which will support “Reference Types”, meaning that these signatures will be able to be directly attached to
a given OCI artifact.</p>
<p>Once this release is finalized, vendors providing registry software will need to update to support those new
specifications, and then update their hosted registries. The jury is out on how long that will take, but we can be
hopeful that it will come sooner rather than later 🤞</p>
<p>If you’d like more of an introduction to Reference Types, I recommend the chainguard blog
<a class="reference external" href="https://www.chainguard.dev/unchained/intro-to-oci-reference-types">here</a>.</p>
</div>
---

## Making a Software Bill of Materials

SBOM is just a structured way to track whats in a software package or image/container in this case. This helps us track any vulns that need tending to.

SCA worked like this for years except now there is a standard format for software composition, such as [SPDX](https://spdx.dev/) and [CycloneDX](https://cyclonedx.org/), which are meant for information exchange (such as between vendors and software consumers). SBOMs can also support enrichment through specifications like [VEX](https://www.ntia.gov/files/ntia/publications/vex_one-page_summary.pdf) (see the nitty gritty on VEX [here](https://docs.oasis-open.org/csaf/csaf/v2.0/csd01/csaf-v2.0-csd01.html#45-profile-5-vex) and a recent more opinionated specification called OpenVEX [here](https://github.com/openvex/spec/blob/main/OPENVEX-SPEC.md)).

We can create SBOM by using syft

```bash 
docker run -v "$(pwd):/tmp" -v /var/run/docker.sock:/var/run/docker.sock anchore/syft:latest docker:example-secure -o json --file example-secure.sbom.json

ls -sh example-secure.sbom.json

jq '.artifacts | length' < example-secure.sbom.json
```

<div> 
<p>This SBOM may not show <em>exactly</em> what ends up running in production.</p>
<p>While dependencies are typically downloaded at build time, processes in containers could technically also download
additional dependencies or make other changes at runtime. In practice, it is significantly less popular and easier to
monitor for/prevent, but something to be aware of.</p>
</div>
![Pasted image 20230509210833.png]]
![Pasted image 20230509210841.png]]

![Pasted image 20230509210848.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509210848.png)


This creates an new file called example-secure.sbom.json with an SBOM of the image finding 143 artifacts. 

Using json out it doesn't use standard it uses SYFT prop format.

---

## Vulnerability Scanning Images

We used JSON out to pass the SBOM to grype for vuln scanning. Both maintained by Anchore.

```bash
docker run -v "$(pwd):/tmp" anchore/grype sbom:example-secure.sbom.json --output json --file example-secure.vulns.json

ls -sh example-secure.vulns.json

jq '.matches | length' < example-secure.vulns.json
```

![Pasted image 20230509211744.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509211744.png)
![Pasted image 20230509211751.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509211751.png)



144 vulns of various severities

Next peer into nginx lite (lite attack surface).

One newer alternative are the chainguard images, built on [wolfi](https://www.chainguard.dev/unchained/introducing-wolfi-the-first-linux-un-distro), which are meant to be free, minimal, secure-by-default, and heavily maintained images that provide a secure foundation for others to build on top of.

Run SBOM generate and vuln scan on chainguard nginx.

```bash
docker run -v "$(pwd):/tmp" -v /var/run/docker.sock:/var/run/docker.sock anchore/syft:latest docker:cgr.dev/chainguard/nginx -o json --file chainguard-nginx.sbom.json
ls -sh chainguard-nginx.sbom.json

jq '.artifacts | length' < chainguard-nginx.sbom.json

docker run -v "$(pwd):/tmp" anchore/grype sbom:chainguard-nginx.sbom.json --output json --file chainguard-nginx.vulns.json

ls -sh chainguard-nginx.vulns.json

jq '.matches | length' < chainguard-nginx.vulns.json
```


![Pasted image 20230509211943.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509211943.png)

![Pasted image 20230509212010.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509212010.png)

![Pasted image 20230509212017.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509212017.png)

![Pasted image 20230509212022.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509212022.png)

0 vulns  not a joke 

Chainguard is so proud of their consistently low vulnerabilities that they provide an [interactive graph](https://visualization-ui.chainguard.app/bar?left=nginx%3alatest&amp;right=cgr.dev%2fchainguard%2fnginx%3alatest) that you can use to compare the `nginx:latest` findings to the `cgr.dev/chainguard/nginx:latest` findings (at least according to [Trivy](https://github.com/aquasecurity/trivy), another popular docker image vulnerability scanning tool).

<div class="admonition note">
<p class="admonition-title">Note</p>
<p>Worried about those “unknown relationship type” errors? I was too, until I found <a class="reference external" href="https://github.com/anchore/grype/issues/1244">this
issue</a> describing that this is due to new information coming from <code class="docutils literal notranslate"><span class="pre">syft</span></code>
that <code class="docutils literal notranslate"><span class="pre">grype</span></code> hasn’t been updated to be able to make use of yet. If you’re running this lab and not seeing those issues,
it means that <code class="docutils literal notranslate"><span class="pre">grype</span></code> has had a release and is now in sync with the <code class="docutils literal notranslate"><span class="pre">syft</span></code> outputs.</p>
</div>

Do we even need example-secure ? Check the nginx image user...

	docker inspect cgr.dev/chainguard/nginx:latest | jq -r '.[].Config.User'

![Pasted image 20230509212156.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509212156.png)

Not root UID 0 

Think about the image and ask more questions before choosing to build on top of one of these secure images. 

To get you pointed in the right direction for some additional investigation, you can use the `cosign tree` command to “Display supply chain security related artifacts for an image such as signatures, SBOMs and attestations”. Let’s take a look at what else the `cgr.dev/chainguard/nginx` image has available:

	docker run cgr.dev/chainguard/cosign tree cgr.dev/chainguard/nginx

![Pasted image 20230509212712.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509212712.png)

 In addition to a signature and SBOM, there are 4 other attestations available for this image that we could use to evaluate and make policy decisions about whether or not we’re comfortable using it in our environment.

---

## Container Image Components

You can make api calls to the registry using the manifest digest.

```bash
mdigest=$(docker inspect --format='{{index .RepoDigests 0}}' example-secure | cut -f2 -d@)

echo $mdigest

curl -k https://localhost:443/v2/example-secure/tags/list

curl -s -k https://localhost:443/v2/example-secure/manifests/$mdigest | sha256sum

curl -s -k https://localhost:443/v2/example-secure/manifests/$mdigest | head -14
```

![Pasted image 20230509212730.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509212730.png)

![Pasted image 20230509212743.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509212743.png)


The sha256 sum is the same in many outputs bc its a content addressable store. Contents returned by the API are the same as its SHA 256 sum.

```bash 
ldigest=$(curl -s -k https://localhost:443/v2/example-secure/manifests/$mdigest | jq -r '.layers[0].digest')

echo $ldigest

curl -s -k https://localhost:443/v2/example-secure/blobs/$ldigest | sha256sum

curl -s -k https://localhost:443/v2/example-secure/blobs/$ldigest | tar -tvzf - | head
```


![Pasted image 20230509213126.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509213126.png)

We can investigate the files that are in this layer.

Docker image layers
https://vsupalov.com/docker-image-layers/

This also means that, just because a file has a certain set of contents at runtime doesn’t mean that’s the _only_ version of that file in the image. You may find a different file in a prior layer that was overwritten by the newer layer.

This is where we can encounter security issues. These files become “hidden” at runtime, but they are very much available in the image itself, if you know where to look, and sometimes they can contain sensitive information such as passwords or keys.

Now the config >

```bash
cdigest=$(curl -s -k https://localhost:443/v2/example-secure/manifests/$mdigest | jq -r '.config.digest')

echo $cdigest

curl -s -k https://localhost:443/v2/example-secure/blobs/$cdigest | sha256sum

curl -s -k https://localhost:443/v2/example-secure/blobs/$cdigest | jq -r '.config.Env[]'

curl -s -k https://localhost:443/v2/example-secure/blobs/$cdigest | jq -r '.history[15]'

```

![Pasted image 20230509213345.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509213345.png)

![Pasted image 20230509213353.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509213353.png)

(Im Getting Tired so Im cheating and Copy Pasting from the Lab Design.)

Just like with the layers, we can see some very interesting information by dissecting an image configuration. In the highlighted lines above we see the environment variables that this image has configured, as well as some of the historical steps taken at build time. Specifically, I am showing the user creation step from earlier in the lab.

This information is available to anybody who can pull the image, and is another place where you may find sensitive information exposed unintentionally. For instance, was a secret passed in at build time and used to pull code from your internal repositories? Or perhaps a secret is needed at runtime to decrypt some files and it’s stored as an environment variable. Both of those are easily exposed to anybody with read access.

The real solution here is to avoid secrets from being stored in your images in the first place. While there are many reasonable approaches to prevent this, I highly recommend [multi-stage builds](https://docs.docker.com/build/building/multi-stage/) and providing secrets at build time safely using [environment variables](https://github.com/moby/buildkit/pull/1534), and dynamically retrieving sensitive information at runtime via integrations with secrets stores like [HashiCorp Vault](https://www.vaultproject.io/), [AWS Secrets Manager](https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html), etc.

<p>As a brief aside, if using <code class="docutils literal notranslate"><span class="pre">curl</span></code> to custom-create API queries isn’t your thing, but you still need to take a peek under
the covers from time to time, I recommend using
<a class="reference external" href="https://github.com/google/go-containerregistry/blob/main/cmd/crane/doc/crane.md"><code class="docutils literal notranslate"><span class="pre">crane</span></code></a>.</p>

<div class="admonition seealso">
<p class="admonition-title">See also</p>
<p>Want more like the above? Well, to start I recommend checking out
<a class="reference external" href="https://raesene.github.io/blog/2023/02/11/Fun-with-Containers-adding-tracking-to-your-images/">this</a> incredibly
interesting blog post about how OCI images can be modified to track when it’s pulled, and anything online from a group
that calls themselves SIG-Honk.</p>
<p>Also, keep an eye out for additional labs by myself in the future 😉</p>
</div>
---

## Container Break Out

Alright, now it’s time for our last section, a container escape.

We’ll start by running a standard Ubuntu container with some additional privileges which are sometimes used when trying to troubleshoot permissions issues:

	docker run -it --privileged ubuntu:20.04


Then, by abusing the additional access from the `--privileged` argument, we can mount the host filesystem, which in my example is on `/dev/xvda1`:

```bash
mount | grep '/dev/'

ls -al /home # Nothing in the home directory in the container

mount /dev/xvda1 /mnt

chroot /mnt
```

![Pasted image 20230509213846.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509213846.png)

Now that we’ve `chroot`ed into that filesystem, we are effectively on the host computer. Let’s see see if we can find anything juicy, and maybe drop a quick backdoor for ourselves later:

```bash
ls -al /home # We can now see /home/ubuntu/ on the host filesystem

sudo useradd hacker

sudo passwd hacker
```

![Pasted image 20230509214110.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509214110.png)

Finally, let’s drop our public key into the `ubuntu` user’s `~/.ssh/authorized_keys` file so there’s another way back in.

![Pasted image 20230509214203.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509214203.png)

Back on the host, we can see evidence of the break-in:

```bash
tail -3 /etc/passwd

tail -1 /home/ubuntu/.ssh/authorized_keys
```

![Pasted image 20230509214246.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509214246.png)

### Fix it!!

How do we prevent these sort of issues? Specific to this breakout, even if we continue to allow `--privileged`, we can mitigate some of the impact by requiring that non-root users be used at runtime. For instance:

	docker run -it -u 1001 --privileged ubuntu:20.04

![Pasted image 20230509214400.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509214400.png)

---

# Kill the EC2 Instance

Don’t forget to terminate your EC2 instance! If you used the CloudFormation templates above, you should be able to go back into your [stacks](https://console.aws.amazon.com/cloudformation/home) and delete the stack that you used to create the lab.

If you aren’t quite ready to terminate your EC2 instance, here are some quick cleanup tasks:

<div class="admonition warning">
<p class="admonition-title">Warning</p>
<p>Running the following commands WILL undo parts of the lab, and should only be used when you are done.</p>
</div>

```D
docker container rm dummy
docker kill registry
docker container rm registry
docker network remove workshop
docker volume rm workshop-certs
rm -f cosign.key cosign.pub
```

![Pasted image 20230509214802.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509214802.png)

![Pasted image 20230509214827.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509214827.png)

Just Delete !!!

![Pasted image 20230509214929.png](https://raw.githubusercontent.com/Xp101T7/Xp101T7.github.io/main/images/Pasted%20image%2020230509214929.png)

Thanks, https://jonzeolla.com/

---

## Links and Resources

* [Container Security 101 — Jon Zeolla documentation](https://jonzeolla.com/labs/container-security-101.html#image-signing)
* [(99+) Jon Zeolla | LinkedIn](https://www.linkedin.com/in/jonzeolla/)
* [Rekor Search](https://search.sigstore.dev/?logIndex=18624203)
* [Implement import & export functionality for volumes · Issue #1436 · docker/cli](https://github.com/docker/cli/issues/1436)
* [Use the Docker command line | Docker Documentation](https://docs.docker.com/engine/reference/commandline/cli/)
* [Share the application | Docker Documentation](https://docs.docker.com/get-started/04_sharing_app/)
* [What is a Container? | Docker](https://www.docker.com/resources/what-container/)
* [hadolint/hadolint: Dockerfile linter, validate inline bash, written in Haskell](https://github.com/hadolint/hadolint)
* [Isolate containers with a user namespace | Docker Documentation](https://docs.docker.com/engine/security/userns-remap/)
* [nginx Tags | Docker Hub](https://hub.docker.com/_/nginx/tags)
* [Docker Registry | Docker Documentation](https://docs.docker.com/registry/)
* [Welcome — Sphinx documentation](https://www.sphinx-doc.org/en/master/)
* [readthedocs/sphinx_rtd_theme: Sphinx theme for readthedocs.org](https://github.com/readthedocs/sphinx_rtd_theme)
* [Home | Read the Docs](https://readthedocs.org/)
* [Specifying stack name and parameters - AWS CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-using-console-create-stack-parameters.html)
* [(563) ChatGPT Changed How I Write Code... - YouTube](https://www.youtube.com/watch?v=gW92DcU1s-U)
* [(563) Using SSH keys on Windows - YouTube](https://www.youtube.com/watch?v=q9YA5H53IHQ)
* [What Are Docker Image Layers? · vsupalov.com](https://vsupalov.com/docker-image-layers/)
