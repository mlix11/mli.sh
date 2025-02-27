+++
date = '2025-02-21T16:44:43+01:00'
draft = true
title = 'Tearing Apart an OCI Image—Just for Fun!'
+++

# Tearing Apart an OCI Image—Just for Fun!

The post is just an exploration from my side to understand technology I am using in more depth and sharing my findings. 

Of course, there are many other blog posts, shell scripts, and resources available. However, my goal here is to try it out myself and share my experience.

Some love it and others hate it. For one it is an easy way to package software, for others it is a security nightmare. 
Containers are everywhere in different abstractions, like Podman, Docker or as a Pod in Kubernetes. 

Every container needs an image to run. I always find it interesting to dig deeper into certain tools. Lately, I thought about downloading an image, extract it and run it. Totally without Docker. 

To obtain and download an image from e.g. registry.docker.com we have to walkthrough some steps.


First of all we have to get the [images index](https://github.com/opencontainers/image-spec/blob/main/image-index.md) images index; the image index is a document which holds the image specific information.
```json
{
  "schemaVersion": 2,
  "mediaType": "application/vnd.oci.image.index.v1+json",
  "manifests": [
    {
      "mediaType": "application/vnd.oci.image.manifest.v1+json",
      "size": 7682,
      "digest": "sha256:5b0bcabd1ed22e9fb1310cf6c2dec7cdef19f0ad69efa1f392e94a4333501270",
      "platform": {
        "architecture": "amd64",
        "os": "linux"
      }
    }

```

Each image tag has a different manifest, and each manifest has the information where to get 
the image for a particular architecture. You can look up the manifest definition [yourself](https://github.com/opencontainers/image-spec/blob/main/manifest.md). 

- code here --

After getting the image index, we can extract and request the manifest for our image. With the manifest (https://github.com/opencontainers/image-spec/blob/main/manifest.md) at hand we can start downloading the layers, and save them as e.g. .tgz on our file system.

-code here-

Each .tgz is representing a layer for our image. The layers have to be extracted in order, because each layer is building up on each another.
When everything is extracted the only missing part is the config.json, which has information about entry point and environment variables. 
When you exported everything in one folder, you can use chroot to use the filesystem. Set the env variables and use your image as it would be in a container. 
While this is a fun exercise to understand OCI images, don't use it in production—it's just chroot, with none of the security protections containers provide.

