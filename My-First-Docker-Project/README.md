# My First Docker Project

So, here I basically created a docker image using an existing image and just modifying it as per my own needs.

Here's what I did:

- Created a `config` directory and a `params.yaml` file.

- Created a `Dockerfile` and used `ros:humble` as an base image.

- Build my image.

Command to build the image:

```bash
docker image build -t <image_name> <dockerfile_path>
```

To view image:

```bash
docker images
```

To share files with container:

```bash
docker run -it -v <path_to_file>:<path_in_container> <image_name>
```