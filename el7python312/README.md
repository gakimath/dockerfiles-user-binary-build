## 1. Build the Docker Image

Run the following command to build the Docker image:

```sh
docker build -t python3.12-centos7 .
```

## 2. Run the Docker Container

Run the Docker container to generate and retrieve the portable Python 3.12 tarball:

```sh
docker run --rm -v $(pwd):/host python3.12-centos7
```

This command mounts the current directory to `/host` inside the container, allowing the container to copy the resulting tarball to your host system.

## 3. Retrieve the Portable Python Package

After the container has finished running, you should find the `python3.12-portable.tar.gz` file in your current directory. This file contains the portable Python 3.12 installation that you can use on EL7-based systems.

## Conclusion

You have successfully built and retrieved a portable Python 3.12 installation for EL7-based distributions. You can now use this package to set up Python 3.12 on similar systems without needing to compile it from source again.
