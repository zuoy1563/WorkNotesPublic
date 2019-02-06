# Maven Debug
 
- [Tutorial here](#tutorial-here)    
- [Cannot recognise parent pom](#cannot-recognise-parent-pom)   
- [Cannot recognise version or validate failure](#cannot-recognise-version-or-validate-failure)

## Tutorial here
First of all, check the tutorial to see if could find any tips.
http://www.runoob.com/maven/maven-tutorial.html

## Cannot recognise parent pom
path is customised thus cannot be automatically found. Add ```<relativePath>../FOLDERNAME/<relativePath/>``` tag underneath ```<parent>```.
The default value of ```<relativePath>``` is ```..```.

## Cannot recognise version or validate failure
>Failure to find org.jfrog.maven.annomojo:maven-plugin-anno:jar:1.4.0 in http://myrepo:80/artifactory/repo was cached in the local repository, resolution will not be reattempted until the update interval of MyRepo has elapsed or updates are forced -> [Help 1]

There are many possibilities, but the nature of the problem is that this dependency cannot be found in the repository. Normally it is not possible when you point the repo to Maven central. For the case that I encountered, the reason was that a specific version cannot be found in the repo - and this is actually because they added some suffix to the ```<version>``` just like ```<version>1.1.1-aabbbcc</version>```. Obviously it is not a real version.