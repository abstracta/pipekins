# pipekins

Simple Groovy script which allows to run declarative `Jenkinsfile`s in local machines, without the need to start [Jenkins](https://jenkins.io/).

By using `pipekins` you can reuse what you have already configured in `Jenkinsfile` to run on `Jenkins` server to run some build steps locally, either to test locally some artifact and debug, or to trace some issue with build steps ran in `Jenkins`.

The main difference between this script and other tools like [shmenkins](https://github.com/qorrect/shmenkins), [jenny](https://github.com/bmustiata/jenny) and [jenkinsfile-runner](https://github.com/jenkinsci/jenkinsfile-runner) is that it supports declarative pipelines and is very lightweight (compared to using jenkinsfile-runner which starts a Jenkins node).

> **Note:**
> As the script does not use any Jenkins library, and only supports features that we have used so far in `Jenkinsfile`s (which we consider are among the most popular ones as well), you should expect it to not work if you use some `Jenkinsfile` step, option, etc, that is not already implemented in the script (you can check the [script code](pipekins) to get an idea of what is supported). 
> We would gladly include contributions for unsupported features and improvements. Refer to [Contributing](#contributing) for some basic instructions.

## Usage

### Pre-requisites

* To use the script you will need [Groovy 2.4+](http://groovy-lang.org/download.html). 
* If you use docker agents in your `Jenkinsfile`, then [Docker 18+](https://docs.docker.com/install/) is required.
* The script has been tested on Mac OS, but should work in Linux systems as well. Script should even work in Windows if all `Jenkinsfile` stages use Docker agents. 

### Installation

To be able to run the script from any location, copy it to a directory included in your OS `PATH` environment variable (e.g: `/usr/local/bin` in Mac OS).

### Execution

Go to a project directory where you have a `Jenkinsfile` and run `pipekins`. This will run all stages in the given `Jenkinsfile`, regardless of current branch and potential conditions specified in the file (since we are currently ignoring and not implementing any logic for conditions).

To only execute certain stages, you can pass as parameters the names of the stages, e.g.: `pipekins build test`.

You can also overwrite defined environment variables with `-e` option. E.g.: `pipekins -e AWS_ACCESS_KEY_ID=XXX -e AWS_SECRET_ACCESS_KEY=XXX deploy`

> This is particularly helpful when using environment variables with `credentials` function, as you can specify the actual value to be used

## Contributing

As previously stated the script supports only `Jenkinsfile`s features that we have used so far, and we are receptive to contributions for improvements and completeness.

In general, when some `Jenkinsfile` section is not supported, the script will fail with a message like the following one:

```
Caught: groovy.lang.MissingMethodException: No signature of method: Jenkinsfile.timeout() is applicable for argument types: (java.util.LinkedHashMap) values: [[time:30, unit:DAYS]]
groovy.lang.MissingMethodException: No signature of method: Jenkinsfile.timeout() is applicable for argument types: (java.util.LinkedHashMap) values: [[time:30, unit:DAYS]]
	at Jenkinsfile$_run_closure1$_closure2.doCall(Jenkinsfile:5)
	at Jenkinsfile$_run_closure1$_closure2.doCall(Jenkinsfile)
	at LocalPiper.options(pipekins:41)
	at Jenkinsfile$_run_closure1.doCall(Jenkinsfile:4)
	at Jenkinsfile$_run_closure1.doCall(Jenkinsfile)
	at LocalPiper.pipeline(pipekins:39)
	at Jenkinsfile.run(Jenkinsfile:2)
	at Jenkinsfile$run.call(Unknown Source)
	at pipekins.run(pipekins:277)
```

In such scenarios, to avoid the script to fail and just ignore the command, is enough to add a method to the `LocalPiper` class with the proper contract. For example:

```
def timeout(Map props) {}
```

Check the [script code](pipekins) to see how other pipeline sections are handled nowadays, and to get an idea on what things are currently supported by the script.
