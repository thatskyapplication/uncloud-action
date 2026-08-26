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
      - uses: actions/checkout@3d3c42e5aac5ba805825da76410c181273ba90b1 # v7.0.1.

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@37fe631027851001ddb9b187196cc803df7f5f0e # v4.3.0.

      - name: Build image
        uses: docker/build-push-action@53b7df96c91f9c12dcc8a07bcb9ccacbed38856a # v7.3.0.
        with:
          context: .
          file: apps/my-app/Dockerfile
          tags: my-app:latest
          load: true

      - name: Deploy via Uncloud
        uses: thatskyapplication/uncloud-action@6e8df783e6dedeb2416d9e15e8b6cfd21ab60e50 # v1.2.2.
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
  uses: thatskyapplication/uncloud-action@6e8df783e6dedeb2416d9e15e8b6cfd21ab60e50 # v1.2.2.
  with:
    image-tag: my-app:latest
    push-only: true
    ssh-private-key: ${{ secrets.SSH_PRIVATE_KEY }}
    server-user: ${{ secrets.SERVER_USER }}
    server-host: ${{ secrets.SERVER_HOST }}
```
