# Reinforcement-Learning

### PYTHON3 in Houdini
this repro is just for learning purposes because its quite a interesting topic =) there will be some use of python3, since there is for it a wide range of ai libs available. 
Sesi doesnt support python3 at the moment but there is the option available to run
it from c++. I am using the boost libraries and it seams to be threadsafe,but you need to compile boost with your python3! [more information](https://www.boost.org/doc/libs/1_66_0/libs/python/doc/html/index.html)


*Usage Example with c++ wrangler*:

DLLs:   
boost_numpy36-vc141-mt-x64-1_69.dll    
boost_python36-vc141-mt-x64-1_69.dll
__________________________
Include:   
#include <boost/python.hpp>   
#include <boost/python/extract.hpp>  
#include <boost/python/numpy.hpp>   
#include <boost/python/tuple.hpp>    
#include <Python.h>   
__________________________
Include Directories:    
BoostFolder/   
Python3Path/include   
__________________________
Link Libraries:   
BoostFolder/stage/lib/boost_python36-vc141-mt-x64-1_69.lib  
BoostFolder/stage/lib/boost_numpy36-vc141-mt-x64-1_69.lib  
Python3Path/libs/python36.lib   
__________________________


```c++
void py3( GU_Detail *gdp) {

        wchar_t *program = Py_DecodeLocale("PATH TO PYTHON3/", NULL);
        Py_SetPythonHome(program);
        Py_Initialize();
        boost::python::numpy::initialize();

			
boost::python::object rand_mod = boost::python::import("HelloWorld");
boost::python::object rand_func = rand_mod.attr("myfunc");
			        
boost::python::tuple shapeA = boost::python::make_tuple(ptCount, 3);
boost::python::numpy::ndarray A = np::zeros(shapeA, np::dtype::get_builtin<double>());
				
for (int i = 0; i != ptCount; i++) {
                UT_Vector3 vec = igdp->getPos3(i);
                A[i][0] = vec.x();
                A[i][1] = vec.y();
                A[i][2] = vec.z();
    }
        
auto b = rand_func(A);
auto x = b[0];         
for (int i = 0; i < 100; i++) {
                std::cout << p::extract<double>(x[i]) << std::endl;
    } 
        
}
        
```
```python
from sklearn.neural_network import MLPRegressor
import numpy as np

def myfunc(pos) :
  XX = np.empty([100, 3])
  XX[:, 0] = pos2[:, 0]
  XX[:, 1] = pos2[:, 1]
  XX[:, 2] = pos2[:, 2]

  YY = np.empty([100, 3])
  YY[:, 0] = pos[:, 0]
  YY[:, 1] = 0
  YY[:, 2] = 0

  clf = MLPRegressor(hidden_layer_sizes = (10, 10),activation = 'tanh', solver = 'lbfgs')
  n = clf.fit(XX, YY)
  test_y = clf.predict(XX)
  return test_y[:, 0], test_y[:, 1], test_y[:, 2]

````






### MatrixLib for Vex  

alternatively you can use https://github.com/jakericedesigns/Houdini-Matrix-Library

Plugin
```c++
void      NxMat		(Array, Row, Cols )					Initalize Matrix
float     NxRows	(Array)							return Number of Rows Matrix
float     NxCols 	(Array)							return Number of Cols Matrix
void      NxSet		(Array,RowNr,ColNr,Value) 				
float     NxGet		(Array,RowNr,ColNr) 			
void   	  NxPrint	(Array)
vector2   NxFind	(Array,value) 						//Findsvalue and returns row col	
array 	  NxGetRow	(Array,ArraySourceRowNr) 				[:,0]
array 	  NxGetCol	(Array,ArraySourceColNr)				[0:,]
void      NxAttrib 	(ArrayTarget,GeoInput,Class,AttributeName,set/add)   	

void	NxTranspose			(ArrayTarget, ArraySource) 
void	NxMultiply  			(ArrayTarget, ArraySourceA, ArraySource B)   A * B
void	NxProduct			(ArrayTarget, ArraySourceA, ArraySource B)   hadmard product 
void	NxInverse			(ArrayTarget, ArraySource)
void	NxAdd				(ArrayTarget, ArraySourceA, ArraySource B)    A+B
void	NxSub				(ArrayTarget, ArraySourceA, ArraySource B)    A-B
void	NxFill				(ArrayTarget, value)
void  	NxPow				(ArrayTarget, value) 
float 	NxSum 				(ArrayTarget)            
float 	NxTrace				(ArrayTarget)	 
````
