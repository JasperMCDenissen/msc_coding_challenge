Created by: Jasper M.C. Denissen
Date: 2026-11-02
email: jasper.denissen@ufz.de

2-week coding challenge
Research question – To what extent can the diurnal amplitude of near-surface air temperature (ΔTa) be explained by remotely sensed soil moisture (SM), vegetation state (Leaf-Area Index; LAI) and relative humidity (HU) upscaled from in-situ observations?

Instructions
The goal of this challenge is to develop the best and leanest (computationally cheap) linear regression model to predict the diurnal amplitude of near-surface air temperature (ΔTa) with hydrometeorological and vegetation variables, across three different locations in Europe. Feel free to use any programming language that suits you best. Feel free to deviate from the prescribed exercises, experiment, and use your intuition. But most of all: enjoy! 
You are not expected to obtain a particular result. I am interested in how you approach the problem, justify your methodological choices, handle the data, and interpret your results.
Prepare a short power point or written report to document your workflow, the (intermediate) results and figures that help you to discuss the questions asked in the following paragraphs. The presentation/report should outline what you did, how you did it and why. Please take care that the code you produce should be reproducible and understandable: I approve of the use of AI for this challenge, but what sets you apart is not the code, but your understanding of it and ability to adapt it to do what you want. 
If you have any questions, feel free to reach out to jasper.denissen@ufz.de.

Data preparation
a) Meteorological and vegetation data
The following data has been downloaded spanning 2010-2011:
1)	E-OBS (ensemble mean, Cornes et al., 2018): https://surfobs.climate.copernicus.eu/dataaccess/access_eobs.php#datafiles
a.	TG: daily mean temperature, 0.25 deg. Regular grid
b.	TN: daily min. temperature, 0.25 deg. Regular grid
c.	TX: daily max. temperature, 0.25 deg. Regular grid
d.	HU: daily mean relative humidity, 0.25 deg. Regular grid
2)	GLASS (Liang et al., 2021): https://glass.hku.hk/archive/LAI/MODIS/0.05D/
a.	LAI: 8-daily mean leaf area index (LAI), 0.05 deg. Regular grid. 
I have temporally averaged the E-OBS data from daily to 8-daily, and I have spatially aggregated the LAI data from 0.05 deg. to 0.25 deg. The data can be found in “best_cells_d8.csv”, which holds the following variables for 3 respective grid cells (see below): 
•	lon: longitude,
•	lat: latitude,
•	date: the date of the respective 8-day period (window)
•	tg: 8-daily mean temperature
•	tn: 8-daily minimum temperature
•	tx: 8-daily maximum temperature
•	hu: 8-daily mean relative humidity
•	lai: 8-daily mean leaf area index 
Inspect the .csv: What are the contents? How do the time series look? Do you recognize a strong seasonal cycle?
The goal of this challenge is to develop a predictive model that can emulate the diurnal amplitude of near-surface air temperature (ΔTa). Compute ΔTa from tx and tn.
The bottleneck for this analysis is the temporal resolution of LAI, which is an 8-day mean. I chose to average all other variables per 8-day period to align them with the LAI data. I could have chosen also to repeat the same LAI value every day, or I could have interpolated the LAI data to obtain daily estimates. Which of the proposed pre-processing methodologies would you prefer and why? 

b) soil moisture data
Now, download 2010 and 2011 of ESA CCI daily 0.25deg Soil moisture v09.2 COMBINED Dorigo et al., 2025): https://catalogue.ceda.ac.uk/uuid/d4e66299f5054129b8076fb7502949e1/. Upon data inspection, you will notice that there are data gaps. Soil moisture obtained from satellites is prone to errors when there are frozen soils or snow, extremely dry soil, or very dense vegetation, which are masked out in preparing this data product. 
Retrieve soil moisture (sm) and its uncertainty (sm_uncertainty) for the three grid points (longitude/latitude combinations) in the best_cells_d8.csv. Then, pre-process the SM data the following way:
•	Read the SM values (“sm”) and the SM uncertainty (“sm_uncertainty”). 
•	Mask out all grid cells which have uncertainties higher than 0.025 m3 m-3.
•	Compute 8-daily averages to fit with the other pre-processed data, only when 5 or more days per 8-day period are available.
Motivate the soil moisture pre-processing steps. 
Add the 8-daily average soil moisture as a column to the .csv. What is so different about the three grid cells in terms of the hydrometeorological and vegetation conditions?

Linear Regression Models
a) with one variable
Develop linear regression models using single variables only (four models in total, one for each LAI, SM, HU and TG) in 2010. Only use data points for which all predictors have values. Evaluate the model using adjusted R² and the statistical significance of the relationship between predictor variable using the p-value. What predictor works best for ΔTa? Are there differences between the grid cells? Why?
Additionally, define the following two extra columns in the .csv:
seas_sin = sin(2*pi * t/365)
seas_cos = cos(2*pi * t/365)
where t (1-365) is the year-of-day of your time series. Visualize these variables. What could these variables describe? Does it make sense to include these in the regression analysis, and why (not)? 
Run the linear regression for seas_sin and seas_cos. Where do they have any explanatory power, and why? 
Now, define a “seasonal model” using seas_sin and seas_cos, and find out how much additional explanatory power each of the other variables (LAI, SM, HU and TG) have by defining “seasonal + variable models” and compute the difference of the adjusted R2 between both models. What predictor offers most explanatory power on top of seasonality, where, and why? 

b) with combination of variables
Now, getting back to the challenge, we can drop seas_cos and seas_sin, as we intend to generate the best and leanest model to predict ΔTa using only real data (LAI, SM, HU and TG). I specifically include the leanest model, as the goal is to do the best we can with the least resources possible. This is particularly useful when working with big data. Define multiple linear regression models for all possible combination of variables, which means that you will have 1-4 predictors. For each model, assess the adjusted R2 and the Akaike Information Criterion (AIC). What is the AIC, and how can it be used to select the best and leanest model?
Compute the ΔAIC, which is the difference between any models’ AIC and the AIC of the best model. Keep only linear regression models that have ΔAIC < 2 and adjusted R2 > 0.36. Are multiple linear regression models retained per grid cell? What does this mean? Are these limits chosen well, or would you change them?
Prediction of ΔTa in 2011
From the selection of models, keep the model that has the least predictors. Good, now we have grid-specific linear regression models! 
Now, use these linear regression models to predict the ΔTa in 2011. How good are the predictions in terms of RMSE? 

Generalized linear regression model
In essence, we are actually not interested in grid-specific models, but in models that can predict across different grid cells. Define the best linear regression model trained on predictors in 2010 all three grid cells, and predict the ΔTa in 2011. How does this generalized model compare to the grid-specific models? How can conditions in 2010 influence prediction skill in 2011? How would you approach this problem if you were to predict ΔTa throughout the whole of Europe? 

Outlook
Discuss how you would continue this analysis, for example with additional training variables, other algorithms, longer study period, including other grid cells/regions, different pre-processing or anything else. What other factors could influence/confound the results? 

Optional: repeat the analysis using XGBoost and discuss whether and why a nonlinear model performs differently.
Acknowledgements
We acknowledge the E-OBS dataset from the EU-FP6 project UERRA (https://www.uerra.eu) and the Copernicus Climate Change Service, and the data providers in the ECA&D project (https://www.ecad.eu)
References
Cornes, R.C., van der Schrier, G., van den Besselaar, E.J.M., Jones, P.D., 2018. An Ensemble Version of the E-OBS Temperature and Precipitation Data Sets. J. Geophys. Res. Atmos. 123, 9391–9409. https://doi.org/10.1029/2017JD028200
Dorigo, W., Preimesberger, W., Hahn, S., Van der Schalie, R., De Jeu, R., Kidd, R., Rodriguez-Fernandez, N., Hirschi, M., Stradiotti, P., Frederikse, T., Gruber, A., Duchemin, D., 2025. ESA Soil Moisture Climate Change Initiative (Soil_Moisture_cci): COMBINED product, Version 09.2. https://doi.org/10.5285/D4E66299F5054129B8076FB7502949E1
Liang, S., Cheng, J., Jia, K., Jiang, B., Liu, Q., Xiao, Z., Yao, Y., Yuan, W., Zhang, X., Zhao, X., Zhou, J., 2021. The Global Land Surface Satellite (GLASS) Product Suite. Bull. Am. Meteorol. Soc. 102, E323–E337. https://doi.org/10.1175/BAMS-D-18-0341.1
