# Java Notes

## Install Java 11 using Brew
```sh
brew install adoptopenjdk11
```

## Update the JAVA_HOME

### Open the bash_profile
```sh
vi ~/.bash_profile
```
### Update the Java 11 Home
```sh
export JAVA_HOME=$(/usr/libexec/java_home -v11)
```
### Reload the .bash_profile
```sh
source ~/.bash_profile
```

## Check the Java version
```sh
java -version
```