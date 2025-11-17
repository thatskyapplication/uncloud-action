# uncloud-action

GitHub Action to deploy Docker images via [Uncloud](https://github.com/psviderski/uncloud). First-party support is the goal!

## Basic example

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
      - uses: actions/checkout@v4

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
          uncloud-profile: my-app
          compise-files: compose.yaml
          ssh-private-key: ${{ secrets.SSH_PRIVATE_KEY }}
          server-user: ${{ secrets.SERVER_USER }}
          server-host: ${{ secrets.SERVER_HOST }}
        env:
          DATABASE_URL: ${{ secrets.DATABASE_URL }}
          API_KEY: ${{ secrets.API_KEY }}
```
