+++
title = "Hugo Blogging with Werecker Auto Build & Deployment"
date = 2019-07-26T01:02:00+01:00
lastmod = 2019-08-15T01:06:40+01:00
tags = ["Hugo", "Git"]
categories = ["TECH"]
draft = false
image = "/img/111.jpeg"
+++

After finishing a blog article, one of the most frequently used process to publish on to website is: save markdown file; preview on local host 1313; generate public file and push to Github or other remote server; push source code to backup. There are two things worthy discussing in this process (1) which is the best way to host html files and source code files (2) which is the better way to automize the procedure.

Hugo requires only a binary file to generate website, with which the update cannot be easier: you just download new .exe file and replace the old one. Still if you are using a similar framework like Hugo or Hexo, keeping tracking source code, forked/cloned theme repo/ html all together can still be a major pain.

Regarding the first issue, Hugo official website along with github documentation has given two way to publish public file. The first way is using Master branch of user.github.io to host /doc (instead of public) folder, which is the easiest one to me. The second way is using gh-pages and the advantage of this method is that allows you to have another branch hosting source code in the same repo. I fail to generate /doc file somehow but it give me a chance to try werecker which surprisingly allows me to achieve the first method with the same advantages of the second method. Long story short, I am now using the Master branch of user.github.io to host public file and dev branch to host source file.

The logic of automated werecker is that you write an article in markdown, add and commit the changes in git, then push the whole blog file source code to remote repo (dev branch in my case). Compared with the process mentioned above, you do NOT need to generate and deploy by yourself any more. Werecker will track your source code on github.io and do that for you. So the key thing is to put the script (werecker.yml) in root file and it tells werecker spercifically how to build and deploy.

The reason of this artile is that the detailed werecker instruction <https://gohugo.io/hosting-and-deployment/deployment-with-wercker/> given by Hugo is an old version. The werecker has changed quite a lot and I spend a while to figure out how to use it. You do not need to search and choose boxes or steps to build and deploy. They can be done in workflow section ![](/img/Hugo blogging with werecker 1.png)

and my sript is shown here:

```yml
# This references a standard debian container from the
# Docker Hub https://registry.hub.docker.com/_/debian/
# Read more about containers on our dev center
# https://devcenter.wercker.com/overview-and-core-concepts/containers/
box: debian
# You can also use services such as databases. Read more on our dev center:
# https://devcenter.wercker.com/administration/services/
# services:
    # - postgres
    # https://devcenter.wercker.com/administration/services/examples/postgresql/

    # - mongo
    # https://devcenter.wercker.com/administration/services/examples/mongodb/

# This is the build pipeline. Pipelines are the core of wercker
# Read more about pipelines on our dev center
# https://devcenter.wercker.com/development/pipelines/
build:
    steps:
    # Steps make up the actions in your pipeline
    # Read more about steps on our dev center:
    # https://devcenter.wercker.com/development/steps/
        - arjen/hugo-build@2.8.0:
            # your hugo theme name
            theme: hugo-theme-cleanwhite
            flags: --buildDrafts=false
deploy:
    steps:
        - install-packages:
            packages: git ssh-client

        - sf-zhou/gh-pages@0.2.6:
            token: $GIT_TOKEN
            domain: sheishe.xyz
            repo: QiKatherine/QiKatherine.github.io
            branch: master
            basedir: public
```

Notice the name 'build' and 'deploy' in the workflow above need to be the same with the name in steps in the werecker.yml file.
