# How to

## Prequisite

Install Hugo

> https://gohugo.io/installation/macos/

## Create a post

```sh
hugo new content posts/post-name.md
```

> https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod

### Post type

https://gohugo.io/getting-started/usage/#draft-future-and-expired-content

## Preview the site

```sh
hugo server -D
```

> -D is short for --buildDrafts

## Publish

```sh
hugo
```

## CI/CD

Base on github action, when push on branch main, it will build and generate the publish files.