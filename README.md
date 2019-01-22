![Info:](https://bytebucket.org/galaxy/galaxy-central/raw/0c1a9c09877422dd03f83ec343b298a9f5d947f6/static/images/icon_info_lrg.gif)


This repository is not maintained anymore, as it has been integrated as core feature into [Galaxy](http://galaxyproject.org). Read more about this in the January 2015 Galaxy [NewsBrief](https://wiki.galaxyproject.org/DevNewsBriefs/2015_01_13) and in our [Community page](https://galaxyproject.org/admin/gies/).


Galaxy IPython Integration
==========================

This projects integrates [IPython Notebook](http://ipython.org/notebook.html), a interactive computational environment, with [Galaxy](http://galaxyproject.org). 
We hope to make Galaxy more attractive for bioinformaticians and to combine the power of both projects to unlock creativity in data analysis, but also in Next-Generation-Training courses.

![IPython in Galaxy](https://raw.githubusercontent.com/bgruening/galaxy-ipython/master/static/images/ipython_in_galaxy.png)

[![IMAGE ALT TEXT HERE](http://img.youtube.com/vi/jQDyTuYnn1k/0.jpg)](http://www.youtube.com/watch?v=jQDyTuYnn1k)


Requirements
============

The only requirement is to have [Docker](https://www.docker.com) installed on your system.
For a detailed instruction how to install docker, please look at the [docker website](https://docs.docker.com/installation/).


Installation
============

Copy the template and config folder in your ``GALAXY_ROOT/config/plugins/visualizations/ipython`` folder and restart Galaxy.
Alternatively, you can clone the repository with

```bash
git clone https://github.com/bgruening/galaxy-ipython.git config/plugins/visualizations/ipython
````

The IPython visualisation option should be visible next to the usual Charts or Trackster options in your visualisation menue.

![Starting IPython in Galaxy](https://raw.githubusercontent.com/bgruening/galaxy-ipython/master/static/images/start_ipython.png)


Features
========

 * run IPython directly in your Galaxy main window or in Galaxy Scratchbook
 * complete encapsulated python environment with matplotlib, pandas and friends installed
 * access to all datasets from your current history via pre-defined IPython function
 * manipulate and plot data as you like and export your new files back into the Galaxy history
 * access your Galaxy instance from IPython with bioblend and redefined functions
 * save the IPython Notebook into your Galaxy history
 * notebook persistence across sessions
 * saved IPython Notebook files can be viewed in HTML and re-opened
 * self-closing and cleaning IPython docker container

How does it work
================

The mako template from the Galaxy visualisation framework renders the interface and builds all files and commands needed to lunch the docker container. A config file is saved under ``/import/`` inside the docker container. The IPython webpage running in docker will be included in a HTML object and displayed to the user. 
Depending on your configuration some JavaScript magic is done to add an object to the DOM, which will be loaded by the browser and conditionally handles login.
Iside of docker TCP connections are monitored using cron. As soon as the user quits using the notebook, the dropping TCP connections are recognised and the cotainer cleans up after itself and kills itself.



Functions and variables
=======================

For your convience, we have added a few pre-defined functions to the IPython profile.

### get

   The get function will copy a dataset, identified by the history_id, from your current Galaxy 
   into the docker container. It will return the file path to the copied file.

   *Example*:
   ```python
      with open( get( 44 ), 'r') as handle:
         for line in handle:
            print( line )
   ``` 

### put

   The put function takes a file path and transfers the file to the current history of your Galaxy session.

   *Example*:
   ```python
      put( './my_file.tsv', file_type = 'tabular')
   ```

### HISTORY_ID

   Accessing your current history is possible with the pre-defined HISTORY_ID variable.
   

### connect to Galaxy

   If you want to communicate with Galaxy you can use ```get_galaxy_connection()``` to get an [bioblend GalaxyInstance object](http://bioblend.readthedocs.org/en/latest/api_docs/galaxy/all.html?highlight=galaxyinstance).

   *Example*:
   ```python
      from bioblend.galaxy.histories import HistoryClient
      gi = get_galaxy_connection()
      hc = HistoryClient( gi )
      for dataset in hc.show_history( HISTORY_ID , contents=True) ):
         print( dataset['id'] )
   ```


Security
========

By default, no security turned on. To do this, you must adjust two variables, `apache_urls` and
`password_auth` in the configuration file (`config/ipython.conf`). 

The `apache_urls` variable should be set to `True`, and containers should be secured via Apache+SSL for production usage. Please see the [setup](INSTALL.md) document for more information.

The `password_auth` variable should be set to `True`. It functions by generating a random password which is only shared with the docker, and the user's browser through Galaxy. In theory this should be a secure system (or at least as secure as the weakest link), however if you find a vulnerability in the system we've devised, please contact us and we'll patch it as soon as possible!


Authors
=======

 * Björn Grüning <bjoern.gruening@gmail.com>
 * Helena Rasche <helena.rasche@gmail.com>


History
=======

- v0.1: Initial public release (01.08.2014)


Licence (MIT)
=============

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
