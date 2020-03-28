<div align="center">
<b>Olivier.codes - JAMStack website</b>
</div>

<br />

<div align="center">

![Build and deploy](https://github.com/olivierloverde/olivier.codes/workflows/Build%20and%20deploy/badge.svg)
[![Dependabot Status](https://api.dependabot.com/badges/status?host=github&repo=olivierloverde/olivier.codes)](https://dependabot.com)
[![CodeFactor](https://www.codefactor.io/repository/github/olivierloverde/olivier.codes/badge)](https://www.codefactor.io/repository/github/olivierloverde/olivier.codes)
[![Security Responsible Disclosure](https://img.shields.io/badge/Security-Responsible%20Disclosure-yellow.svg)](https://github.com/nodejs/security-wg/blob/master/processes/responsible_disclosure_template.md)
[![js-standard-style](https://img.shields.io/badge/code%20style-standard-brightgreen.svg?style=flat)](http://standardjs.com/)

</div>

<br />

This is the source code of my blog.

### Technical stack
Tooling
- [Node 12 LTS](https://nodejs.org/)
- [Yarn](https://yarnpkg.com/)

Static blog generator
- [Hexo](https://hexo.io/)
- [Cactus theme](https://github.com/probberechts/hexo-theme-cactus)

Automation
- [Github Actions](https://github.com/features/actions])
- [GitHub Actions for GitHub Pages](https://github.com/peaceiris/actions-gh-pages)

### Usage
#### Local developments
```
$ hexo server
```

#### Update online website 
```
$ git commit -am 'XXX' && git push
```