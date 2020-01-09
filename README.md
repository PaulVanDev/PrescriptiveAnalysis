# Prescriptive Analysis  (or Machine Learning Control)

The very inspirational graph below is well know among the data science community. It first describes the development of analytics through the decades - descriptive analytics before 00s, diagnostic analytics and the focus on data mining after 00s, more emphase on predictive analytics with the emergence of data science and the democratization of DS tools, prescriptive analytics for the future... - or it can also describe the process workflow of a data science project in function of the effort and time that it's possible to put in. Depending on whether you have a week or months, the insights can come from a good first statistical analysis or a full predictive analysis.

However if the first three kind of analytics are relatively obvious, the last one - prescriptive analytics - often rise questions. What is it? How to do that?

<a href="pictures/Capture_prescriptive.PNG"><img class="fig" src="pictures/Capture_prescriptive.PNG" style="width:30%; height:auto;"/></a>


## What is prescriptive analysis?

In certain situations, once a good predictive model is available, we would like to know what to do to have a particular output or more generally to be able to control it. Let's take the well-know problem of the price houses prediction. Once the model is available, one could ask what are the actionable levers to increase the price of a house depending of the model entries. Obviously some variables can't be easily change as the mean price of the area, the ground surface, but for instance a better heat insulation could be considered.
In the industry, if the goal is not to predict but to control a performance, a yield, prescriptive analysis will help to find out how to move the actionable (or also controllable) variables and how to change them. It is a kind of automation with a multivariate perspective. Again in this case, some parameters can not be changed (external temperature, weather, specific process parameters), so a distinction between controllable parameters and free parameters has to be done.

## How to do prescriptive analysis?

There's several ways to do this but mainly it can be considered and solved as an optimizing problem. The goal would be to find how to modify the entry parameters to converge to the desired output. This is represented in the following view. A real physical system is joined as it would be for an AI application in production, the interest is indeed to optimize a real output, not the one of the model.
In this context, we suppose that the predictive model is a digital twin of the real system in the sens that it can simulate relatively well the system behavior.
The predictive model is packed in an optimizer. The optimizer receives the order - the change to apply -, the controllable variables and their range of variation. The output is the optimized variables (the recommanded controllable inputs joined to the free inputs).
Note that the problems considered here are regressions.

<a href="pictures/Capture_MLC.PNG"><img class="fig" src="pictures/Capture_MLC.PNG" style="width:30%; height:auto;"/></a>

## How to do prescriptive analysis in Python?

Everyone in the Data Science Community knows Scikit-learn, but the solution for prescriptive analytics is not there. It is in Scipy.
A first attempt was done with SCIPY OPTIMIZE MINIMIZE. It was working well for most of the common regression models, SVR, MLP... but not for decision tree based regression models. This is due to the conditional nature of the decision tree based models, a proper math function is required for that kind of optimisation.
A second attempt was done with SCIPY OPTIMIZE DIFFERENTIAL_EVOLUTION for generalizing to all regression models. 


```
from scipy.optimize import differential_evolution
def predict_max_evol(_fitted_model,_x0,_variable_features,_deviation): 
    def model(Xin):
        X=[Xin]
        #print(X)
        return -1*_fitted_model.predict(X)
    #test if fitted_model is regression modeling
    buf = "%s\n" % _fitted_model
    if "egress" in buf or "SVR" in buf:
        if "ogistic" in buf :
            return "LogisticRegression - Not a regression model"
        else:
            #build bnds based on x0, variable_features  and deviation
            bnds=[];
            count=0
            for i in _x0.keys():
                if i in _variable_features:
                    bound_min=_x0[i]-(_x0[i]*_deviation)
                    bound_max=_x0[i]+(_x0[i]*_deviation)
                    bnds.insert(count,(bound_min,bound_max))
                    count+=1
                else:
                    bnds.insert(count,(_x0[i],_x0[i]))
                    count+=1
            #print(bnds)
       ###### MAIN CALL ######     
            result = differential_evolution(model,bounds=bnds)
       #######################
            #print(result)
            dfx0 = _x0.to_frame()
            
            dfx0.rename(columns={ dfx0.columns[0]: "x0" },inplace=True)
            dfx0['x0_max']=result.x
            dfx0['delta'] = dfx0.apply(lambda x: x['x0_max']-x['x0'], axis=1 )   
            return([-1*result.fun],dfx0)
    else:
        return "Not a regression model"
```
In this code, we see the main call where we find the optimizing function, the model packed in a internal defined function, and bounds which are the range of variations of all parameters. If a parameter is controllable, the bounds are previously calculated thanks to a deviation coefficient passed in argument.
In output of the global function, we find the expected optimized result and the recommended value for each parameters.

Outside this function, it could be necessary to control the recommended values to avoid an exceeding of the functional ranges.
Further, and in a real context, a full management taken into account the time constants of the physical system should be considered.

The view below shows the results of a test. 5 parameters were initially build and melted in a mathematical function to create an output. A predictive model was trained on a first part of the series of points and tested on the rest. In this last part, we activate at a defined moment the optimization, asking how to change param1 and param2 in order to maximize the output. The recommandation is to decrease param1 and param2.


<a href="pictures/Capture_MLC_result.PNG"><img class="fig" src="pictures/Capture_MLC_result.PNG" style="width:30%; height:auto;"/></a>
