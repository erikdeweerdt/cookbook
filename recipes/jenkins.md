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

## Kill orphaned runs

In very specific cases, it may happen that a build for a deleted project remains in the queue. Jenkins will try to run it, but fail without aborting the build.
These builds can't easily be aborted because _they don't really exist_.

Following Groovy snippet allows to kill such jobs:
```groovy
import org.jenkinsci.plugins.workflow.support.steps.ExecutorStepExecution.PlaceholderTask
import org.jenkinsci.plugins.workflow.job.WorkflowRun

def busyExecutors = Jenkins.instance.computers
                                .collect { 
                                  c -> c.executors.findAll { it.isBusy() }
                                }
                                .flatten() // reminder: transforms list(list(executor)) into list(executor)

busyExecutors.each { e ->
  // update below to some filter matching all and only the jobs you want to kill
  if (e.toString().contains("FILTER")) {
    Queue.Task task = e.getCurrentExecutable()?.getParent()
    if (task instanceof PlaceholderTask) {
      WorkflowRun r = (WorkflowRun)((PlaceholderTask)task).run()
      r.doKill()
    }
  }
}

return
```
