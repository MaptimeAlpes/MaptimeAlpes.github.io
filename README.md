Maptime-Alpes Page
============

localized english/french blog-style jekyll site.

(need to install jekyll: ```gem install jekyll```)

The primary branch is called _develop_ and the published branch is _master_.

Github pages does not support plugins, so the contents of the _site dir need to be pushed to the master branch in order to use the plugin's functionality.

All edits and work need to be done on the _develop_ branch.

Including Images? Make sure to use [TinyPNG](https://tinypng.com/).

Using rake for builds and deploys to help with the unique build scenario.

(need to install rake: ```gem install rake```)

to build the site in dev mode (with jekyll server and code watcher)
```rake build:dev```

to build the site for production (no server, just build)
```rake build:pro```

to clear out the _site dir
```rake delete```

to preview site in default browser
```rake preview```

to commit and deploy (what I do every time I publish something)
```rake commit_deploy```

to commit/push changes to develop branch without the site deploy
```rake commit```

to deploy _site dir to master branch (which published GitHub page) without committing first
```rake deploy```


============
Maptime-Alpes.com (c) by Maptime-Alpes contributors (https://github.com/orgs/MaptimeAlpes/people)

Maptime-Alpes.com is licensed under a
Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International (CC BY-NC-SA 4.0) License.

View license at <http://creativecommons.org/licenses/by-nc-sa/4.0/>.