"# prescriptive_analysis" 

The very inspirational graph below is well know among the data science community. It first describes the development of analytics through the decades - descriptive analytics before 00s, diagnostic analytics and the focus on data mining after 00s, more emphase on predictive analytics with the emergence of data science and the democratization of DS tools, prescriptive analytics for the future... - or it can also describe the process workflow of a data science project in function of the effort and time that it's possible to put in. Depending on whether you have a week or months, the insights can come from a good first statistical analysis or a full predictive analysis.

However if the first three kind of analytics are relatively obvious, the last one - prescriptive analytics - often rise questions. What is it? How to do that?

<a href="pictures/Capture_prescriptive.PNG"><img class="fig" src="pictures/Capture_prescriptive.PNG" style="width:30%; height:auto;"/></a>


What is prescriptive analysis?

In certain situations, once a good predictive model is available, we would like to know what to do to have a particular output or more generally to be able to control it. Let's take the well-know problem of the price houses prediction. Once the model is available, one could ask what are the actionable levers to increase the price of a house depending of the model entries. Obviously some variables can't be easily change as the mean price of the area, the ground surface, but for instance a better heat insulation could be considered.
In the industry, if the goal is not to predict but to control a performance, a yield, prescriptive analysis will help to find out how to move the actionable (or also controllable) variables and how to change them. It is a kind of automation with a multivariate perspective. Again in this case, some parameters can not be changed (external temperature, weather, specific process parameters), so a distinction between controllable parameters and free parameters has to be done.

How to do prescriptive analysis?

There's several ways to do this but mainly it can be considered and solved as an optimizing problem. The goal would be to find how to modify the entry parameters to converge to the desired output. This is represented in the following view. A real physical system is joined as it would be for an AI application in production, the interest is indeed to optimize a real output, not the one of the model.
In this context, we suppose that the predictive model is a digital twin of the real system in the sens that it can simulate relatively well the system behavior.
The predictive model is packed in an optimizer. The optimizer receives the order - the change to apply -, the controllable variables and their range of variation. The output is the optimized variables (the recommanded controllable inputs joined to the free inputs).




<a href="pictures/Capture_MLC.PNG"><img class="fig" src="pictures/Capture_MLC.PNG" style="width:30%; height:auto;"/></a>

<a href="pictures/Capture_MLC_result.PNG"><img class="fig" src="pictures/Capture_MLC_result.PNG" style="width:30%; height:auto;"/></a>
