# iWander
### Dynamics of Interstellar Wanderers

[![arXiv](http://img.shields.io/badge/arXiv-1711.09397-orange.svg?style=flat)](http://arxiv.org/abs/1711.09397)

Presentation
------------

This package is an implementation of the general method devised by
Zuluaga et al. (2017) for assesing the origin of interstellar small
bodies (asteroids and comets).

The package include a series of databases and tools that can be used
in general for studying the dynamics of an interstellar vagabond
object (small-body, interstellar spaceship and even stars).

If you use or adapt the package for any scientific purpose please
refer to this paper:

> Zuluaga,Sánchez-Hernández, Sucerquia & Ignacio Ferrin, A general
> method for assesing the procedence of an interstellar small body:
> the case of 1I/´Oumuamuaa (1I/2017 U1),
> [arXiv:1711.09397](https://arxiv.org/abs/1711.09397).

The paper is under review for publication in the Astronomical Journal.

> **NOTE** As the revision process is undergoing, un updated version
  of the paper will be available in [this
  link](doc/Zuluaga_et_al_2017-AssesingOriginProbability.pdf).

Progenitor Candidates
----------------------

A list of the progenitor candidates of the interstellar object
**1I/2017 U1 (Oumuamua)**, identified using this method along with
their corresponding origin probabilities are available (and will be
updated) [in this file](CANDIDATES-Oumuamua.md).

> **NOTE**: This list could change when better astrometric information
  or improvements on the methodology be available. Stay tuned!

Getting the package
-------------------

You may obtain the package in different ways:

- Get a "compact" version of the package and the associated data (650
  MB) ready to be used from [this link](http://bit.ly/iWander)

- Cloning anonymously the github repository:

```  
git clone http://github.com/seap-udea/iWander.git
```  

- Download the tarball:

```  
wget http://github.com/seap-udea/iWander/archive/master.zip
```  

- Cloning the package as developer (permissions required):

```  
git clone git@github.com:seap-udea/iWander.git
```  

The size of the package is large (several hundreds of MBs).  This is
mainly due to the data required to run some of the modules (GAIA and
Radial velocities databases, SPICE Kernels, etc.).  

Before you start
----------------

Before you start using the package set up the `makefile`.  Create a
local copy of `compiler.in.temp`:

```  
cp compiler.in.temp compiler.in
```  

Edit the file to properly choose your system architecture.  You just
need to comment/uncomment the proper line in the file:

```  
###################################################
#CHOOSE YOUR ARCHITECTURE
###################################################
#ARCH=32
ARCH=64
```  

Test it:

```  
make
```  

Unpacking large files
---------------------

Large files are splitted in 20MB chunks of data inside the `.store`
directory.  Before you use some critical modules you must unpack those
large files:

```  
make unpack
```  

Quickstart
----------

1. Edit the general configuration files ``iwander.conf`` and
   ``wanderer.conf`` for setting up the name of the wanderer and its
   properties.

2. Compile the key programs:

   ```  
   make all
   ```  

2. Generate the surrogate objects and propagate them until the time of ingress.

   ```  
   ./wanderer.exe
   ```  

3. Compute the minimum distance to all the stars in the input catalog
   and select the candidates.

   ```  
   ./encounters.exe
   ```  

4. Find progenitor candidates and compute their origin probability:

   ```  
   ./probability.exe
   ```  

The output of this process is the file ``progenitors-<wanderer>.csv``
having a list of the progenitor candidates with their respective
origin probability.

Structure of the package
------------------------

The package is made of three type of components: programs, scripts and
databases.

Programs are used to compute the core functions (propagate wanderers,
find encounters, compute interstellar origin probabilities, etc.)

Scripts are used for pre and post processing of the information
required or produced by the package.  Here we mainly use python
scripts and Ipython notebooks.

Databases contain the information required to run some of the
functionalities of the package.

Components
----------

- **wanderer**: This program integrate the orbit of a moving object
  inside the Solar System.
  
  * Function: 

    This program perform three different tasks:

    1) Calculate the time t_asymp when the single conic approximation is
       good enough to predict the future position of the interstellar
       object.

    2) Calculate the time t_ingress at which the object was at a half
       of the truncation tidal radius of the Solar System, ie. 100,000
       AU.

    3) Predict the position and velocity of the surrogate objects at
       t_ingress.

  * Input: None

  * Output: 

    + wanderer.csv

      Rows: 1 is for nominal solution, the rest is for random particle

      Cols: 

      ```
	  0:NUmber of the object (0 for nominal trajectory) 
	  1-6:Initial random elements, q,e,i,W,w,Mo,to,mu
	  7-12:Asymptotic elements, q,e,i,W,w,Mo,to,mu
	  13:Time of ingress to Solar System
	  14-19:Cartesian position at ingress wrt. Ecliptic J2000
	  20-25:Cartesian position at ingress wrt. J2000
	  26-31:Cartesian position at ingress wrt. Galactic
	  32:Radiant at ingress RA(h) (terminal)
	  33:Radiant at ingress DEC(deg)
	  34:Radiant at ingress l(deg)
	  35:Radiant at ingress b(deg)
      ```

- **encounters**: This program integrate the orbit of a moving object
  inside the Solar System.
  
  * Function: 

    This program perform two different tasks:

    1) Compute the LMA minimum distance and time to all stars in the
       AstroRV catalogue..

    2) Select the progenitor candidates.

  * Input: 
    + wanderer.csv

  * Output: 

    + encounters.csv: all the columns of the input catalog (AstroRV)
      plus:

      Cols:

      ```
	  0: n
	  1-6: Position and velocity of the star for LMA purposes
	  7: Initial distance of the star, d
	  8: Minimum LMA distance, dmin
	  9: Minimum LMA time, tmin
	  10-13: Relative velocity computed with LMA vrelx,vrely,vrelz,vrel,
	  14-...: All fields in AstroRV catalog
      ```

    + candidates.csv

      Cols:

      ```
	  0: n
	  1-6: Position and velocity of the star for LMA purposes
	  7: Initial distance of the star, d
	  8: Minimum LMA distance, dmin
	  9: Minimum LMA time, tmin
	  10-13: Relative velocity computed with LMA vrelx,vrely,vrelz,vrel,
	  14-...: All fields in AstroRV catalog
      ```

- **probability**: This program integrate the orbit of a moving object
  inside the Solar System.

  * Function: calculate the IOP for a list of candidates.

  * Input:
    + wanderer.csv
    + candidates.csv

  * Output: 
    + progenitors.csv

      Cols:

      ```
      0: IOP for this candidate, Pprob
      1: Average position probability, Psmed
      2: Average velocity probability, Pvmed
      3: Probability distance factor, fdist
      4-6: Nominal minimum time, minimum distance, relative velocity
      7,8: Minimum and maximum tmin
      9,10: Minimum and maximum dmin
      11,12: Minimum and maximum vrel
      13...: Same as candidates.csv
      ```

For the developer
-----------------

iWander uses GSL and Spice as backbone utility libraries.  The latest
precompiled version of both libraries, along witth the header files
are provided with the package in the `util` directory.


Changes log
-----------

The first version of the software was that published along the
submission of the paper to the arXiv. We call it the 0.1 version.

Since that initial version we have performed improvements on the
package either suggested by the reviewers of the paper, by colleagues
or by the community.  These are the most important
ones in the available versions of the manuscript.

### v0.1


Initial version.

### v0.2

Post-submission version.  These are the changes introduced to the
package:

* A new global configuration file ``iwander.conf`` was created that is
  common to all programs.  This configuration file now include the
  name of the analysed wanderer, eg. Oumuamua, Voyager1, etc.

* The output files from the main programs have as suffix the name of
  the wanderer, eg. wanderer-Oumuamua.csv, candidates-Oumuamua.csv.

* We have included new rules to makefile: all, cleandata, cleanall.

Acknowledgements
----------------

This package has been developed thanks to the incredible work made by
previous scientist and developers. Most of the work of those who make
this package possible has been cited in our papers.  Others are
mentioned in the software itself.

License
--------------
Copyright (C) 2017 Jorge I. Zuluaga, Oscar Sanchez-Hernandez, Mario Sucerquia & Ignacio Ferrin

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and the databases associated (the "Package"),
to deal in the Package without restriction, including without
limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Package, and to
permit persons to whom the Package is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Package.  A
reference to the Package shall be included in all scientific
publications that make use of the Package.

THE PACKAGE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS
OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE
LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION
OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION
WITH THE DATABASE OR THE USE OR OTHER DEALINGS IN THE DATABASE.