# Reinforcement-Learning

### PYTHON3 in Houdini
this repro is just for learning purposes because its quite a interesting topic =) there will be some use of python3, since there is for it a wide range of ai libs available. 
Sesi doesnt support python3 at the moment but there is the option available to run
it from c++. I am using the boost libraries and it seams to be threadsafe,but you need to compile boost with your python3! [more information](https://www.boost.org/doc/libs/1_66_0/libs/python/doc/html/index.html)


*Usage Example with c++ wrangler*:

```c++

void curvefitting( GU_Detail *Geo,const char *str) {

//Stash Stuff
GU_Detail *gdp = new GU_Detail;
gdp->duplicate(*Geo);
news->stashAll();
int ptCount = gdp->getNumPoints();

//Python3 Initialization
wchar_t *program = Py_DecodeLocale("YOURPYTHON3PATH", NULL);
Py_SetPythonHome(program);
Py_Initialize();
np::initialize();

//Load Python File_________________________ 
p::object rand_mod = boost::python::import("Fitt");
p::object rand_func = rand_mod.attr("Curve");
        
//HOUDINI -> BOOST                            
p::tuple shapeA = boost::python::make_tuple(ptCount, 3);
np::ndarray A = np::zeros(shapeA, np::dtype::get_builtin<double>());
                                
for (int i = 0; i != ptCount; i++) {
                UT_Vector3 vec = gdp->getPos3(i);
                A[i][0] = vec.x();
                A[i][1] = vec.y();
                A[i][2] = vec.z();
}
          
//Run Python3 File     
auto b = rand_func(A,ptCount,100);

//BOOST -> HOUDINI   
auto x = b[0];
auto y = b[1];
auto z = b[2];        
      
//SetPosition   
for (int i = 0; i < ptCount; i++) {
                i = Geo->appendPoint();
                Geo->setPos3(i, UT_Vector3F(
                   p::extract<double>(x[i]),
                   p::extract<double>(y[i]),
                   p::extract<double>(z[i])
                 ));                    
}

```
```python
from sklearn.neural_network import MLPRegressor
import numpy as np

def myfunc(A,size,hidenA) :
	XX = np.empty([size, 3])
	XX[:, 0] = A[:, 0]
	XX[:, 1] = A[:, 1]
	XX[:, 2] = A[:, 2]
	
	YY = np.empty([size, 3])
	YY[:, 0] = 0
	YY[:, 1] = 0
	YY[:, 2] = A[:, 2]
	
	clf = MLPRegressor(alpha=0.001, hidden_layer_sizes = (hidenA,hidenA), max_iter = 50000, 
        activation = 'tanh', learning_rate = 'adaptive',solver='lbfgs')
	
	n = clf.fit(YY, XX)
	test_y = clf.predict(YY)
	return test_y[:, 0], test_y[:, 1], test_y[:, 2]

````
![Image of Yaktocat](https://i.ibb.co/QYxhVYT/fit.png)



Dynamic-Links:
boost_numpy36-vc141-mt-x64-1_69.dll    
boost_python36-vc141-mt-x64-1_69.dll

![Image of Yaktocat](https://i.ibb.co/8xmdnLD/Unbenannts.png)





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
