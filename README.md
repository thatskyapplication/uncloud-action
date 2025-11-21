# uncloud-action

GitHub Action to deploy Docker images via [Uncloud](https://github.com/psviderski/uncloud). First-party support is the goal!

## Basic example

### Deploying

This is a full flow!

```YAML
name: Deploy to production
on:
  push:
    branches:
      - main
permissions:
  contents: read
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Build image
        uses: docker/build-push-action@v6
        with:
          context: .
          file: apps/my-app/Dockerfile
          tags: my-app:latest
          load: true

      - name: Deploy via Uncloud
        uses: thatskyapplication/uncloud-action@v1
        with:
          image-tag: my-app:latest
          compose-files: compose.yaml
          uncloud-profile: my-app
          ssh-private-key: ${{ secrets.SSH_PRIVATE_KEY }}
          server-user: ${{ secrets.SERVER_USER }}
          server-host: ${{ secrets.SERVER_HOST }}
        env:
          DATABASE_URL: ${{ secrets.DATABASE_URL }}
          API_KEY: ${{ secrets.API_KEY }}
```

### Pushing only the image

To push an image to a server without deploying, set `push-only: true`.

```YAML
- name: Push image to server
  uses: thatskyapplication/uncloud-action@v1
  with:
    image-tag: my-app:latest
    push-only: true
    ssh-private-key: ${{ secrets.SSH_PRIVATE_KEY }}
    server-user: ${{ secrets.SERVER_USER }}
    server-host: ${{ secrets.SERVER_HOST }}
```
