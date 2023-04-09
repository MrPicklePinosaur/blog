
# blog

yet another blog rewrite

## RUNNING FOR DEVELOPMENT

this blog is powered by [zola](https://getzola.org), you can serve the blog with
```sh
zola serve
```

## GITHUB ACTIONS SETUP

Add the github actions secret `KUBERNETES_SERVER_URL` with the value obtained
from running the following command in your kubernetes cluster
```sh
kubectl config view --minify -o 'jsonpath={.clusters[0].cluster.server}'
```

Also add the secret `KUBERNETES_SECRET` 

## TODO

- [ ] consider making site multilinugal (or maybe have language be a tag, since I don't wanna translate every post)
- [x] stylize site
- [ ] add search
