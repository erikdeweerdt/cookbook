# Jenkins
## Useful Groovy snippets
Run a command and print out _both_ STDOUT and STDERR:
```groovy
StringBuffer b = new StringBuffer()
// Don't use consumeProcessOutput as that's non-blocking
"java -version".execute().waitForProcessOutput(b, b)
println b
```
Decrypt a secret:
```groovy
// Note that secrets aren't portable between Jenkins instances
// You can only decrypt secrets stored on the Jenkins instance you run this on
println hudson.util.Secret.decrypt("{ABC}")
```
Perform DNS lookup:
```groovy
InetAddress[] dnsresult = InetAddress.getAllByName("google.com");

dnsresult.each {
  print it
}

return
```
