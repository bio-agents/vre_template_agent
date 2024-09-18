Requirements and Installation
=============================

Requirements
------------

Software
^^^^^^^^

- Python 3.6+
- Git

Python Modules
^^^^^^^^^^^^^^

- `openvre-agent-api <https://github.com/inab/openvre-agent-api>`_

Agent API. This API provides a standard way for all agents to be wrapped to allow for a common interface layer.

Installation
------------

Directly from GitHub:

.. code:: console

   git clone https://github.com/inab/vre_template_agent.git
   cd vre_template_agent

Create Python environment:

.. code:: console

   python3 -m venv venv
   source venv/bin/activate
   pip install --upgrade wheel
   pip install -r requirements.txt


Documentation
-------------

To build the documentation:

.. code:: console

   cd docs
   pip install -r requirements.txt
   make html

Documentation will be generated (in HTML format) inside the ``_build/html`` directory.