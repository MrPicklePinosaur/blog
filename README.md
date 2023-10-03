
# blog

yet another blog rewrite

## RUNNING FOR DEVELOPMENT

this blog is powered by [zola](https://getzola.org), you can serve the blog with
```sh
just serve
```

## GITHUB ACTIONS SETUP

Add the github actions secret `KUBERNETES_SERVER_URL` with the value obtained
from running the following command in your kubernetes cluster
```sh
kubectl config view --minify -o 'jsonpath={.clusters[0].cluster.server}'
```

Also add the secret `KUBERNETES_SECRET` 

## DEPLOYMENT

Currently, this blog is deployed onto my own kubernetes cluster. To push the
latest changes to the cluster, build the blog container and push the image to
github's container registry. The entire build a push process is automated as a
github action.

## TODO

- [ ] consider making site multilinugal (or maybe have language be a tag, since I don't wanna translate every post)
- [x] stylize site
- [ ] add search
