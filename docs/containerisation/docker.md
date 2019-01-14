# Docker

<p align="center">
  <img src="https://media.giphy.com/media/CB4FhLQiJX1QI/giphy.gif"/>
</p>

## Docker Commands

The -it flag will allow you to ctrl+c to close the running image
`docker run -p 3000:3000 -it 0319d6e6a683`

The -d flag will run service in the background allowing continued terminal access
`docker run -p 3000:3000 -d 0319d6e6a683`

To push an image to Docker Hub (required for Kubernetes access)
```bash
docker login
docker tag imageid your-login/docker-demo
docker push your-login/docker-demo
```

To tag an image during the build process
```bash
docker build -t your-login/docker-demo .
docker push your-login/docker-demo
```
