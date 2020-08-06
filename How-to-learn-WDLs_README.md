# How to learn WDL

WDL scripts require an execution environment to run. Also you may choose to use other language tools, such as a linter.  You have a number of choices for these tools, see this [link](https://github.com/openwdl/wdl#software-and-tools) for detail. WDL Lesson examples in this course use...
1. **GCP** services
    - GCE Virtual Machine(s) for compute and GCS bucket(s) for file storage --or-- 
    - Terra.bio workflows (Terra runs on GCP)
2. **cromwell** for execution
    - cromwell `run` mode via GCE, single VM --or--
    - cromwell `server` mode via Terra.bio, multiple GCE VMs
        - uses Google Cloud Life Sciences API [or Pipeline/PAPI API]
3. **womtool** for linting and input parameter file generation
4. example **WDL files** from this repository (also includes sample data files)
5. **docker** for containers used to run bioinformatics tools (GATK, others...) or scripts

Links to other resources
 - How to set up GCE VM for WDL - [link](https://github.com/openwdl/learn-wdl/blob/master/5_reference_material/2_WDL-dev-env.md#wdl-dev-env)
 - WDL keywords/syntax reference - [link](https://github.com/openwdl/learn-wdl/blob/master/5_reference_material/1_WDL-concepts.md)
 - Learn WDL screencasts (YouTube playlist) - [link](https://www.youtube.com/playlist?list=PL4Q4HssKcxYv5syJKUKRrD8Fbd-_CnxTM)

### 📺Click below to WATCH 'Learn WDL' series of 10 min screencasts on YouTube

[![Learn WDL Playlist](/images/learn-wdl-screencasts.png)](https://www.youtube.com/playlist?list=PL4Q4HssKcxYv5syJKUKRrD8Fbd-_CnxTM "Welcome to Learn WDL")


---

## WDL Script Execution Syntax Examples

###  Used in this Course
#### For Cromwell / Womtool
- if running WDL script with **cromwell**
    - example `java -jar cromwell-XY.jar run ./path/file.wdl` 
    - select `run` (single workflow) or `sever` (multiples) mode
        - run mode  `java -jar cromwell-XY.jar run ./path/file.wdl` 
        - run mode using specified input file  `java -jar cromwell-XY.jar run ./path/file.wdl --inputs ./path/input.json`
        - server mode  `java -jar -server cromwell-XY.jar run ./path/server.wdl`
- if running WDL script with **womtool**
    - example  `java -jar womtool-XY.jar <action> ./path/file.wdl` 
    - actions include the following:
        - `validate` (WDL syntax & semantics)
        - `inputs` (create JSON input file)
        - `highlight` (reformat/color WDL)
        - `parse` (WDL syntax only)
        - `graph` (outputs a .dot of WDL DAG)
        - `womgraph` (print a graph <.dot> file)
---

### 📺Click below to WATCH 'WDL Script Execution and Linting' (7 min) on YouTube

[![WDL Script Linting](/images/wdl-lint.png)](https://www.youtube.com/watch?v=FnwfmmJMF3Q "WDL Linting")

---

### Other Options for WDL Script Execution

#### For WDL_Runner for PAPI
- if running on public cloud, can use `wdl_runner` - [link](https://wdl-runner.readthedocs.io/en/latest/GettingStarted/TutorialSteps/)  this uses cromwell and also GCP Life Sciences (or Pipelines) API to allocate resources (VMs) on GCP  
    - example: 
        `gcloud alpha genomics pipelines run --pipeline-file wdl_pipeline.yaml `
                `--regions us-central1 --inputs-from-file WDL=test-wdl/ga4ghMd5.wdl,`
                `WORKFLOW_INPUTS=test-wdl/ga4ghMd5.inputs.json,`
                `WORKFLOW_OPTIONS=test-wdl/basic.papi.us.options.json `
                `--env-vars WORKSPACE=gs://YOUR-BUCKET/wdl_runner/work,`
                `OUTPUTS=gs://YOUR-BUCKET/wdl_runner/output `
                `--logging gs://YOUR-BUCKET/wdl_runner/logging`
    - When submitted using the Pipelines API, the workflow runs on multiple Google Compute Engine virtual machines. 
    
    - First a master node is created for Cromwell, and then Cromwell submits each stage of the workflow as one or more separate pipelines. 
    
    - Execution of a running Pipeline proceeds as:

        - Create Compute Engine virtual machine
        - On the VM, in a Docker container, execute wdl_runner.py
        - Run Cromwell (server)
        - Submit workflow, inputs, and options to Cromwell server
        - Poll for completion as Cromwell executes:
        - Call `pipelines.run()` to execute call 1
        - Poll for completion of call 1
        - Call `pipelines.run()` to execute the next call
        - Poll for completion of the next call
        - Repeat steps 3-4 until all WDL "calls" complete>
        - Copy workflow metadata to output path
        - Copy workflow outputs to output path
        - Destroy Compute Engine Virtual machine

#### For Miniwdl
- if testing, can use `miniwdl` - [link](https://github.com/chanzuckerberg/miniwdl) - requires Docker
    - `pip install miniwdl` --or-- `conda install miniwdl`
    - miniwdl check `miniwdl run_self_test` - verifies miniwdl is working
    - miniwdl check `miniwdl check --path path/myWdl.wdl` - lints WDL
    - miniwdl input checker `miniwdl run hello.wdl` shows missing inputs
    - miniwdl param test run `miniwdl run hello.wdl who=Lynn "who=SoCalDevGal" x=42` runs with test inputs

#### For Cromshell
- if testing submitting one or more workflows to a cromwell server, can use `cromshell` - [link](https://github.com/broadinstitute/cromshell) - requires Docker
    - `brew install broadinstitute/dsp/cromshell` --or-- `conda install cromshell`
    - also requires `column`, `curl`, `mail` and `jq`
    - cromshell run `cromshell submit workflow.wdl inputs.json` - can set timeout values, can start/stop/abort workflows
        - can get status, metadata, execution-status-count, timing (timing diagram in a browser), logs, fetch-logs...more



