.. currentmodule:: agent.VRE_Agent


Creating a agent
---------------

Before you start creating your agent, the basic entity `Agent` of the `basic_modules` from the openvre-agent-api_ library
is imported.

.. _openvre-agent-api: https://github.com/inab/openvre-agent-api

::

    from basic_modules.agent import Agent

The `Agent` is the core element to running an application within the VRE. It defines the procedures that need to happen
to prepare the data along with the application to run over chunks of data provided. An application can be either a piece
of code that is written in Python or R that is run with given chunks of data or defined parameters. The results are then returned
to the calling method :meth:`~agent.VRE_Agent.myAgent.agentExecution()`.

All agents contain at least a `run()` method which takes the input files, defined output files, and relevant metadata. Returned by
the run method is the output files and their metadata.

All the agents should be placed within the `agents` directory within the package.

Your first `myAgent`
~~~~~~~~~~~~~~~~~~~~~

Use the ``agent/VRE_Agent.py`` script as a template to create you new agent.

The script contains `myAgent` a class that you can define to run your application. You must define the location of your
application to run, e.g. ``/example/hello.py``.

You can see the first lines of `myAgent` class saved in a file named ``VRE_agent.py`` under the ``agent/`` directory from
the project’s top level folder. In this class, we define the application we want to run using the wrapper. The
application can be implemented in different programming languages, such as Python or R, taking into account how to
execute it. To see how to execute your application see `Command line agent`_ and `Adding software dependencies`_ sections.

::

    class myAgent(Agent):
        """
        This class define <myAgent> Agent.
        """
        DEFAULT_KEYS = ['execution', 'project', 'description']
        """config.json default keys"""
        PYTHON_SCRIPT_PATH = "/example/hello.py"
        """<myApplication>"""

    ... (omitted for brevity)

`myAgent` class defines two attributes and some methods. Specifically:

* :attr:`~agent.VRE_Agent.myAgent.DEFAULT_KEYS`: identifies default arguments from openVRE configuration file (``config.json``).

* :attr:`~agent.VRE_Agent.myAgent.PYTHON_SCRIPT_PATH`: location of your application that you wanna run with the wrapper. You can use a relative or absolute path.

* :meth:`~agent.VRE_Agent.myAgent.run()`: `TO BE DOCUMENTED` ...

* :meth:`~agent.VRE_Agent.myAgent.agentExecution()`: `TO BE DOCUMENTED` ...


Adding input files
~~~~~~~~~~~~~~~~~~

If your application expects one or more input files, you must add them as follows in the method
:meth:`~agent.VRE_Agent.myAgent.agentExecution()`:

::

    input_file_1 = input_files.get('hello_file')
    if not os.path.isabs(input_file_1):
        input_file_1 = os.path.normpath(os.path.join(self.parent_dir, input_file_1))

For each input file, you must add a new variable. The VRE works with absolute paths and as you can see, it is
preferable to check if the directory is absolute or not; which in case it is not, becomes absolute.

Adding arguments
~~~~~~~~~~~~~~~~

If your application expects one or more arguments, you must add them as follows in the method
:meth:`~agent.VRE_Agent.myAgent.agentExecution()`:

::

    argument_1 = self.arguments.get('username')
    if argument_1 is None:
        errstr = "argument_1 must be defined."
        logger.fatal(errstr)
        raise Exception(errstr)

For each argument, you must add a new variable and validate their importation from openVRE configuration file (``config.json``)
to ensure the execution of the application.

Adding output files
~~~~~~~~~~~~~~~~~~~

If your application expects one or more output files, you must add them as follows in the method
:meth:`~agent.VRE_Agent.myAgent.agentExecution()`:

::

    output_id = output_metadata[0]['name']
    output_type = output_metadata[0]['file']['file_type'].lower()
    output_file_path = glob(self.execution_path + "/*." + output_type)[0]
    if os.path.isfile(output_file_path):
        output_files[output_id] = [(output_file_path, "file")]

For each output file, you only need to extract its `identifier`, `file type`, and `file path` from the output_metadata that
contains the information from openVRE configuration file (``in_metadata.json``); and save it to `output_files`. As you can see,
we recommend to check the existence of each output file.

.. note:: If your application generates multiple output files, we recommend to develop a separate method. You can see an example HERE_.

.. _HERE: https://github.com/inab/vre_cwl_executor/blob/9449f73e0c1e7ac53bc454c40884b0540e96b4a6/cwl/workflow.py#L93-L144.

Command line agent
~~~~~~~~~~~~~~~~~

::

    cmd = [
        'python',
        self.parent_dir + self.PYTHON_SCRIPT_PATH,  # hello.py
        input_file_1,  # hello.txt
        argument_1,  # username
    ]


.. currentmodule:: VRE_RUNNER


Adding software dependencies
----------------------------

If you need some software requirements to run your application, you must add them to the file VRE_RUNNER_, in the
project's top level directory.

.. _VRE_RUNNER: https://github.com/inab/vre_template_agent/blob/master/VRE_RUNNER

::

    DEPENDENCIES=("Rscript", "docker")

Integrating the new agent into VRE
---------------------------------

The final step is the integration of the agent into VRE. The X section should provide a guide to the initial JSON files.
The full JSON specifications is located in this GoogleDoc. the details the requirements for correctly creating the Agent
description JSON file and the requirements for parameters needed for an application.
