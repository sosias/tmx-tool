# tmx-tool

Really basic TMX ([Translation Memory eXchange](http://xml.coverpages.org/tmxSpec971212.html)) to csv exporter and in browser viewer. This tool now can only visualize the translations in a table and export them as a csv file

Use the tool at [https://sosias.github.io/tmx-tool](https://sosias.github.io/tmx-tool)

The separator in the exported csv file is the tab character

## Project setup
```
yarn install
```

### Compiles and hot-reloads for development
```
yarn serve
```

### Compiles and minifies for production
```
yarn build
```

### Run your unit tests
```
yarn test:unit
```

### Lints and fixes files
```
yarn lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/)

## Publish to github pages

```
git checkout --orphan gh-pages
npm run build
git --work-tree dist add --all
git --work-tree dist commit -m 'Deploy'
git push origin HEAD:gh-pages --force
rm -r dist
git checkout -f master
git branch -D gh-pages
```
