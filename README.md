# Coal Price Analysis Project

![Coal Price Analysis](https://static.gujaratsamachar.com/content_image/content_image_a7a4a921-2907-42b8-85f0-ea2b497e9ee2.jpeg)

## Project Overview

This project focuses on analysing the volatility in coal price due to factors like supply & demand dynamics, geopolitical events and changes in government policies which makes businesses difficult to acccurately predict the future price. The main objective of the project is to minimize coal procurement cost. By analysing the data to identify trends and patterns so that we can find the solution for the business problem, this project aims to provide real-time insights for better decision-making.

## Table of Contents

- [Project Overview](#project-overview)
- [Business Problem](#business-problem)
- [Business Objective](#business-objective)
- [Business Constraint](#business-constraint)
- [Tech Stack](#tech-stack)
- [Features](#features)
- [Usage](#usage)
- [Dashboard](#interactive-dashboards)
- [Project Structure](#project-structure)


## Business Problem

Coal prices can be highly volatile due to factors such as supply and demand dynamics, geopolitical events, changes in government policies, and fluctuations in currency exchange rates. This volatility makes it difficult for businesses to accurately predict future prices, leading to potential revenue and profit fluctuations.

## Business Objective

Minimize coal procurement cost.

## Business Constraint

Global market volatility and uncertainty because of war like scenarios, might drastically impact the coal prices.

## Tech Stack

- **Python**
- **MySQL**
- **Matplotlib**
- **Seaborn**
- **Power BI**
- **Looker Studio**


## Features

- **Data Cleaning and Preprocessing**: Identification and treatment of outliers, transforming the data, handling of missing values, and ensuring data consistency.
- **Data Exploration and Analysis**: In-depth analysis using Matplotlib and Seaborn to uncover patterns and insights.
- **Interactive Dashboards**: Dynamic dashboards created with Power BI, Looker Studio, and Excel for effective data visualization and reporting.

  

## Usage

1. Load the dataset:
    ```python
      # Pushing the Raw data into Mysql database 
        import pandas as pd

        import numpy as np
        
        from sqlalchemy import create_engine
        
        import pymysql
        
        engine=create_engine("mysql+pymysql://{user}:{pw}@localhost/{db}"
                             .format(user = 'root',
                                     pw = 'password',
                                     db = 'coal'))
        
        ### We are trying to load the original file directly into Python
        raw_data=pd.read_csv("C:/Users/Naveen/Desktop/Project_Team 196/merged_economic_data.csv")
        
        ### The loaded raw data is pushed into the mysql database
        raw_data.to_sql('merged_economic_data', con=engine, if_exists='replace', chunksize=None, index=False)
        
        sql= 'select * from merged_economic_data'
        
        data=pd.read_sql_query(sql, engine)
    ```
<img src="https://github.com/NaveenM-10/Coal-Price-Analysis/blob/main/Coal%20Price%20Analysis/Screenshot%202024-09-10%20172153.png"/>
From the Above image we can see that there are 977 records and 28 features in the dataset.

2. Run the data cleaning and preprocessing scripts:
    ```python
         #####           DATA PRE-PROCESSING             #####
          ## FILLING THE MISSING VALUES IN THE NUMERICAL COLUMNS USING THE FORWORD FILL
          num_missing_treated=data[['Coal_RB_4800_FOB_London_Close_USD', 'Coal_RB_5500_FOB_London_Close_USD',
                 'Coal_RB_5700_FOB_London_Close_USD','Coal_RB_6000_FOB_CurrentWeek_Avg_USD',
                 'Coal_India_5500_CFR_London_Close_USD', 'Price_WTI', 'Price_Brent_Oil',
                 'Price_Dubai_Brent_Oil', 'Price_ExxonMobil', 'Price_NTPC','Price_Shenhua', 'tempmax_RB', 
                 'tempmin_RB', 'temp_RB', 'dew_RB', 'humidity_RB', 'precip_RB', 'windspeed_RB','tempmax_Limpopo', 'tempmin_Limpopo', 'temp_Limpopo', 'dew_Limpopo',
                 'humidity_Limpopo', 'precip_Limpopo', 'windspeed_Limpopo']].ffill()
          
          ## MISSING DATA IN CATEGORICAL DATA IS REPLACED WITH NO RAIN
          cat_missing_treated=data[['preciptype_RB','preciptype_Limpopo']].fillna('No rain')
          
          ## CONCATING THE BOTH THE VARIABLES INTO NEW DATAFRAME
          data_no_missing = pd.concat([data[['Date']], num_missing_treated, cat_missing_treated], axis=1)
          
          ## PRICE_NTPC HAS A NAN IN THE FIRST ROW HENCE BFILL IS USED
          data_no_missing['Price_NTPC']=data_no_missing[['Price_NTPC']].bfill()
          
          ### IDENTIFYING THE OUTLIERS IN THE DATA
          
          iqr_Coal_RB_4800_FOB_London_Close_USD=data_no_missing['Coal_RB_4800_FOB_London_Close_USD'].quantile(0.75)
          -data_no_missing['Coal_RB_4800_FOB_London_Close_USD'].quantile(0.25)
          
          ul_Coal_RB_4800_FOB_London_Close_USD=data_no_missing['Coal_RB_4800_FOB_London_Close_USD'].quantile(0.75)
          +1.5*iqr_Coal_RB_4800_FOB_London_Close_USD
          
          ll_Coal_RB_4800_FOB_London_Close_USD=data_no_missing['Coal_RB_4800_FOB_London_Close_USD'].quantile(0.25)
          -1.5*iqr_Coal_RB_4800_FOB_London_Close_USD
          
          outliers_Coal_RB_4800_FOB_London_Close_USD = data_no_missing[(data_no_missing['Coal_RB_4800_FOB_London_Close_USD'] < ll_Coal_RB_4800_FOB_London_Close_USD) | 
                          (data_no_missing['Coal_RB_4800_FOB_London_Close_USD'] > ul_Coal_RB_4800_FOB_London_Close_USD)]
          
          ### Coal_RB_5500_FOB_London_Close_USD
          iqr_Coal_RB_5500_FOB_London_Close_USD=data_no_missing['Coal_RB_5500_FOB_London_Close_USD'].quantile(0.75)
          -data_no_missing['Coal_RB_5500_FOB_London_Close_USD'].quantile(0.25)
          
          ul_Coal_RB_5500_FOB_London_Close_USD=data_no_missing['Coal_RB_5500_FOB_London_Close_USD'].quantile(0.75)
          +1.5*iqr_Coal_RB_5500_FOB_London_Close_USD
          
          ll_Coal_RB_5500_FOB_London_Close_USD=data_no_missing['Coal_RB_5500_FOB_London_Close_USD'].quantile(0.25)
          -1.5*iqr_Coal_RB_5500_FOB_London_Close_USD
          
          outliers_Coal_RB_5500_FOB_London_Close_USD = data_no_missing[(data_no_missing['Coal_RB_5500_FOB_London_Close_USD'] < ll_Coal_RB_5500_FOB_London_Close_USD) | 
                          (data_no_missing['Coal_RB_5500_FOB_London_Close_USD'] > ul_Coal_RB_5500_FOB_London_Close_USD)]
          
          nan_Coal_RB_5500_FOB_London_Close_USD=pd.DataFrame(np.where(data_no_missing['Coal_RB_5500_FOB_London_Close_USD'] < ll_Coal_RB_5500_FOB_London_Close_USD, np.nan, 
                          np.where(data_no_missing['Coal_RB_5500_FOB_London_Close_USD'] > 
                                   ul_Coal_RB_5500_FOB_London_Close_USD, np.nan,
                                   data_no_missing['Coal_RB_5500_FOB_London_Close_USD']))) 
          
          
          
          ### Coal_RB_5700_FOB_London_Close_USD
          iqr_Coal_RB_5700_FOB_London_Close_USD=data_no_missing['Coal_RB_5700_FOB_London_Close_USD'].quantile(0.75)
          -data_no_missing['Coal_RB_5700_FOB_London_Close_USD'].quantile(0.25)
          
          ul_Coal_RB_5700_FOB_London_Close_USD=data_no_missing['Coal_RB_5700_FOB_London_Close_USD'].quantile(0.75)
          +1.5*iqr_Coal_RB_5700_FOB_London_Close_USD
          
          ll_Coal_RB_5700_FOB_London_Close_USD=data_no_missing['Coal_RB_5700_FOB_London_Close_USD'].quantile(0.25)
          -1.5*iqr_Coal_RB_5700_FOB_London_Close_USD
          
          ### Coal_RB_6000_FOB_CurrentWeek_Avg_USD
          iqr_Coal_RB_6000_FOB_CurrentWeek_Avg_USD=data_no_missing['Coal_RB_6000_FOB_CurrentWeek_Avg_USD'].quantile(0.75)
          -data_no_missing['Coal_RB_6000_FOB_CurrentWeek_Avg_USD'].quantile(0.25)
          
          ul_Coal_RB_6000_FOB_CurrentWeek_Avg_USD=data_no_missing['Coal_RB_6000_FOB_CurrentWeek_Avg_USD'].quantile(0.75)
          +1.5*iqr_Coal_RB_6000_FOB_CurrentWeek_Avg_USD
          
          ll_Coal_RB_6000_FOB_CurrentWeek_Avg_USD=data_no_missing['Coal_RB_6000_FOB_CurrentWeek_Avg_USD'].quantile(0.25)
          -1.5*iqr_Coal_RB_6000_FOB_CurrentWeek_Avg_USD
          
          ### Coal_India_5500_CFR_London_Close_USD
          iqr_Coal_India_5500_CFR_London_Close_USD=data_no_missing['Coal_India_5500_CFR_London_Close_USD'].quantile(0.75)
          -data_no_missing['Coal_India_5500_CFR_London_Close_USD'].quantile(0.25)
          
          ul_Coal_India_5500_CFR_London_Close_USD=data_no_missing['Coal_India_5500_CFR_London_Close_USD'].quantile(0.75)
          +1.5*iqr_Coal_India_5500_CFR_London_Close_USD
          
          ll_Coal_India_5500_CFR_London_Close_USD=data_no_missing['Coal_India_5500_CFR_London_Close_USD'].quantile(0.25)
          -1.5*iqr_Coal_India_5500_CFR_London_Close_USD
          
          ### Price_WTI
          iqr_Price_WTI=data_no_missing['Price_WTI'].quantile(0.75)
          -data_no_missing['Price_WTI'].quantile(0.25)
          
          ul_Price_WTI=data_no_missing['Price_WTI'].quantile(0.75)
          +1.5*iqr_Price_WTI
          
          ll_Price_WTI=data_no_missing['Price_WTI'].quantile(0.25)
          -1.5*iqr_Price_WTI
          
          ### Price_Brent_Oil
          iqr_Price_Brent_Oil=data_no_missing['Price_Brent_Oil'].quantile(0.75)
          -data_no_missing['Price_Brent_Oil'].quantile(0.25)
          
          ul_Price_Brent_Oil=data_no_missing['Price_Brent_Oil'].quantile(0.75)
          +1.5*iqr_Price_Brent_Oil
          
          ll_Price_Brent_Oil=data_no_missing['Price_Brent_Oil'].quantile(0.25)
          -1.5*iqr_Price_Brent_Oil
          
          ### Price_Dubai_Brent_Oil
          iqr_Price_Dubai_Brent_Oil=data_no_missing['Price_Dubai_Brent_Oil'].quantile(0.75)
          -data_no_missing['Price_Dubai_Brent_Oil'].quantile(0.25)
          
          ul_Price_Dubai_Brent_Oil=data_no_missing['Price_Dubai_Brent_Oil'].quantile(0.75)
          +1.5*iqr_Price_Dubai_Brent_Oil
          
          ll_Price_Dubai_Brent_Oil=data_no_missing['Price_Dubai_Brent_Oil'].quantile(0.25)
          -1.5*iqr_Price_Dubai_Brent_Oil
          
          ### Price_ExxonMobil
          iqr_Price_ExxonMobil=data_no_missing['Price_ExxonMobil'].quantile(0.75)
          -data_no_missing['Price_ExxonMobil'].quantile(0.25)
          
          ul_Price_ExxonMobil=data_no_missing['Price_ExxonMobil'].quantile(0.75)
          +1.5*iqr_Price_ExxonMobil
          
          ll_Price_ExxonMobil=data_no_missing['Price_ExxonMobil'].quantile(0.25)
          -1.5*iqr_Price_ExxonMobil
          
          ### Price_NTPC
          iqr_Price_NTPC=data_no_missing['Price_NTPC'].quantile(0.75)
          -data_no_missing['Price_NTPC'].quantile(0.25)
          
          ul_Price_NTPC=data_no_missing['Price_NTPC'].quantile(0.75)
          +1.5*iqr_Price_NTPC
          
          ll_Price_NTPC=data_no_missing['Price_NTPC'].quantile(0.25)
          -1.5*iqr_Price_NTPC
          
          ### Price_Shenhua
          iqr_Price_Shenhua=data_no_missing['Price_Shenhua'].quantile(0.75)
          -data_no_missing['Price_Shenhua'].quantile(0.25)
          
          ul_Price_Shenhua=data_no_missing['Price_Shenhua'].quantile(0.75)
          +1.5*iqr_Price_Shenhua
          
          ll_Price_Shenhua=data_no_missing['Price_Shenhua'].quantile(0.25)
          -1.5*iqr_Price_Shenhua
          
          ### tempmax_RB
          iqr_tempmax_RB=data_no_missing['tempmax_RB'].quantile(0.75)
          -data_no_missing['tempmax_RB'].quantile(0.25)
          
          ul_tempmax_RB=data_no_missing['tempmax_RB'].quantile(0.75)
          +1.5*iqr_tempmax_RB
          
          ll_tempmax_RB=data_no_missing['tempmax_RB'].quantile(0.25)
          -1.5*iqr_tempmax_RB
          
          ### tempmin_RB
          iqr_tempmin_RB=data_no_missing['tempmin_RB'].quantile(0.75)
          -data_no_missing['tempmin_RB'].quantile(0.25)
          
          ul_tempmin_RB=data_no_missing['tempmin_RB'].quantile(0.75)
          +1.5*iqr_tempmin_RB
          
          ll_tempmin_RB=data_no_missing['tempmin_RB'].quantile(0.25)
          -1.5*iqr_tempmin_RB
          
          ### temp_RB
          iqr_temp_RB=data_no_missing['temp_RB'].quantile(0.75)
          -data_no_missing['temp_RB'].quantile(0.25)
          
          ul_temp_RB=data_no_missing['temp_RB'].quantile(0.75)
          +1.5*iqr_temp_RB
          
          ll_temp_RB=data_no_missing['temp_RB'].quantile(0.25)
          -1.5*iqr_temp_RB
          
          ### dew_RB
          iqr_dew_RB=data_no_missing['dew_RB'].quantile(0.75)
          -data_no_missing['dew_RB'].quantile(0.25)
          
          ul_dew_RB=data_no_missing['dew_RB'].quantile(0.75)
          +1.5*iqr_dew_RB
          
          ll_dew_RB=data_no_missing['dew_RB'].quantile(0.25)
          -1.5*iqr_dew_RB
          
          ### humidity_RB
          iqr_humidity_RB=data_no_missing['humidity_RB'].quantile(0.75)
          -data_no_missing['humidity_RB'].quantile(0.25)
          
          ul_humidity_RB=data_no_missing['humidity_RB'].quantile(0.75)
          +1.5*iqr_humidity_RB
          
          ll_humidity_RB=data_no_missing['humidity_RB'].quantile(0.25)
          -1.5*iqr_humidity_RB
          
          ### precip_RB
          iqr_precip_RB=data_no_missing['precip_RB'].quantile(0.75)
          -data_no_missing['precip_RB'].quantile(0.25)
          
          ul_precip_RB=data_no_missing['precip_RB'].quantile(0.75)
          +1.5*iqr_precip_RB
          
          ll_precip_RB=data_no_missing['precip_RB'].quantile(0.25)
          -1.5*iqr_precip_RB
          
          ### windspeed_RB
          iqr_windspeed_RB=data_no_missing['windspeed_RB'].quantile(0.75)
          -data_no_missing['windspeed_RB'].quantile(0.25)
          
          ul_windspeed_RB=data_no_missing['windspeed_RB'].quantile(0.75)
          +1.5*iqr_windspeed_RB
          
          ll_windspeed_RB=data_no_missing['windspeed_RB'].quantile(0.25)
          -1.5*iqr_windspeed_RB
          
          ### tempmax_Limpopo
          iqr_tempmax_Limpopo=data_no_missing['tempmax_Limpopo'].quantile(0.75)
          -data_no_missing['tempmax_Limpopo'].quantile(0.25)
          
          ul_tempmax_Limpopo=data_no_missing['tempmax_Limpopo'].quantile(0.75)
          +1.5*iqr_tempmax_Limpopo
          
          ll_tempmax_Limpopo=data_no_missing['tempmax_Limpopo'].quantile(0.25)
          -1.5*iqr_tempmax_Limpopo
          
          ### tempmin_Limpopo
          iqr_tempmin_Limpopo=data_no_missing['tempmin_Limpopo'].quantile(0.75)
          -data_no_missing['tempmin_Limpopo'].quantile(0.25)
          
          ul_tempmin_Limpopo=data_no_missing['tempmin_Limpopo'].quantile(0.75)
          +1.5*iqr_tempmin_Limpopo
          
          ll_tempmin_Limpopo=data_no_missing['tempmin_Limpopo'].quantile(0.25)
          -1.5*iqr_tempmin_Limpopo
          
          ### temp_Limpopo
          iqr_temp_Limpopo=data_no_missing['temp_Limpopo'].quantile(0.75)
          -data_no_missing['temp_Limpopo'].quantile(0.25)
          
          ul_temp_Limpopo=data_no_missing['temp_Limpopo'].quantile(0.75)
          +1.5*iqr_temp_Limpopo
          
          ll_temp_Limpopo=data_no_missing['temp_Limpopo'].quantile(0.25)
          -1.5*iqr_temp_Limpopo
          
          ### dew_Limpopo
          iqr_dew_Limpopo=data_no_missing['dew_Limpopo'].quantile(0.75)
          -data_no_missing['dew_Limpopo'].quantile(0.25)
          
          ul_dew_Limpopo=data_no_missing['dew_Limpopo'].quantile(0.75)
          +1.5*iqr_dew_Limpopo
          
          ll_dew_Limpopo=data_no_missing['dew_Limpopo'].quantile(0.25)
          -1.5*iqr_dew_Limpopo
          
          ### humidity_Limpopo
          iqr_humidity_Limpopo=data_no_missing['humidity_Limpopo'].quantile(0.75)
          -data_no_missing['humidity_Limpopo'].quantile(0.25)
          
          ul_humidity_Limpopo=data_no_missing['humidity_Limpopo'].quantile(0.75)
          +1.5*iqr_humidity_Limpopo
          
          ll_humidity_Limpopo=data_no_missing['humidity_Limpopo'].quantile(0.25)
          -1.5*iqr_humidity_Limpopo
          
          ### precip_Limpopo
          iqr_precip_Limpopo=data_no_missing['precip_Limpopo'].quantile(0.75)
          -data_no_missing['precip_Limpopo'].quantile(0.25)
          
          ul_precip_Limpopo=data_no_missing['precip_Limpopo'].quantile(0.75)
          +1.5*iqr_precip_Limpopo
          
          ll_precip_Limpopo=data_no_missing['precip_Limpopo'].quantile(0.25)
          -1.5*iqr_precip_Limpopo
          
          ### windspeed_Limpopo
          iqr_windspeed_Limpopo=data_no_missing['windspeed_Limpopo'].quantile(0.75)
          -data_no_missing['windspeed_Limpopo'].quantile(0.25)
          
          ul_windspeed_Limpopo=data_no_missing['windspeed_Limpopo'].quantile(0.75)
          +1.5*iqr_windspeed_Limpopo
          
          ll_windspeed_Limpopo=data_no_missing['windspeed_Limpopo'].quantile(0.25)
          -1.5*iqr_windspeed_Limpopo
          
          
          #### UPDATING THE OUTLIERS WITH NAN
          data_no_missing['Coal_RB_4800_FOB_London_Close_USD']=pd.DataFrame(np.where(data_no_missing['Coal_RB_4800_FOB_London_Close_USD']
                                          < ll_Coal_RB_4800_FOB_London_Close_USD, np.nan, 
                          np.where(data_no_missing['Coal_RB_4800_FOB_London_Close_USD'] > 
                                   ul_Coal_RB_4800_FOB_London_Close_USD, np.nan,
                                   data_no_missing['Coal_RB_4800_FOB_London_Close_USD']))) 
          
          data_no_missing['Coal_RB_5500_FOB_London_Close_USD']=pd.DataFrame(np.where(data_no_missing['Coal_RB_5500_FOB_London_Close_USD']
                                          < ll_Coal_RB_5500_FOB_London_Close_USD, np.nan, 
                          np.where(data_no_missing['Coal_RB_5500_FOB_London_Close_USD'] > 
                                   ul_Coal_RB_5500_FOB_London_Close_USD, np.nan,
                                   data_no_missing['Coal_RB_5500_FOB_London_Close_USD']))) 
          
          data_no_missing['Coal_RB_5700_FOB_London_Close_USD']=pd.DataFrame(np.where(data_no_missing['Coal_RB_5500_FOB_London_Close_USD']
                                          < ll_Coal_RB_5500_FOB_London_Close_USD, np.nan, 
                          np.where(data_no_missing['Coal_RB_5500_FOB_London_Close_USD'] > 
                                   ul_Coal_RB_5500_FOB_London_Close_USD, np.nan,
                                   data_no_missing['Coal_RB_5500_FOB_London_Close_USD']))) 
          
          data_no_missing['Coal_RB_6000_FOB_CurrentWeek_Avg_USD']=pd.DataFrame(np.where(data_no_missing['Coal_RB_6000_FOB_CurrentWeek_Avg_USD']
                                          < ll_Coal_RB_6000_FOB_CurrentWeek_Avg_USD, np.nan, 
                          np.where(data_no_missing['Coal_RB_6000_FOB_CurrentWeek_Avg_USD'] > 
                                   ul_Coal_RB_6000_FOB_CurrentWeek_Avg_USD, np.nan,
                                   data_no_missing['Coal_RB_6000_FOB_CurrentWeek_Avg_USD']))) 
          
          data_no_missing['Coal_India_5500_CFR_London_Close_USD']=pd.DataFrame(np.where(data_no_missing['Coal_India_5500_CFR_London_Close_USD']
                                          < ll_Coal_India_5500_CFR_London_Close_USD, np.nan, 
                          np.where(data_no_missing['Coal_India_5500_CFR_London_Close_USD'] > 
                                   ul_Coal_India_5500_CFR_London_Close_USD, np.nan,
                                   data_no_missing['Coal_India_5500_CFR_London_Close_USD']))) 
          
          data_no_missing['Price_WTI']=pd.DataFrame(np.where(data_no_missing['Price_WTI']
                                          < ll_Price_WTI, np.nan, 
                          np.where(data_no_missing['Price_WTI'] > 
                                   ul_Price_WTI, np.nan,
                                   data_no_missing['Price_WTI']))) 
          
          data_no_missing['Price_Brent_Oil']=pd.DataFrame(np.where(data_no_missing['Price_Brent_Oil']
                                          < ll_Price_Brent_Oil, np.nan, 
                          np.where(data_no_missing['Price_Brent_Oil'] > 
                                   ul_Price_Brent_Oil, np.nan,
                                   data_no_missing['Price_Brent_Oil']))) 
          
          data_no_missing['Price_Dubai_Brent_Oil']=pd.DataFrame(np.where(data_no_missing['Price_Dubai_Brent_Oil']
                                          < ll_Price_Dubai_Brent_Oil, np.nan, 
                          np.where(data_no_missing['Price_Dubai_Brent_Oil'] > 
                                   ul_Price_Dubai_Brent_Oil, np.nan,
                                   data_no_missing['Price_Dubai_Brent_Oil']))) 
          
          data_no_missing['Price_ExxonMobil']=pd.DataFrame(np.where(data_no_missing['Price_ExxonMobil']
                                          < ll_Price_ExxonMobil, np.nan, 
                          np.where(data_no_missing['Price_ExxonMobil'] > 
                                   ul_Price_ExxonMobil, np.nan,
                                   data_no_missing['Price_ExxonMobil']))) 
          
          data_no_missing['Price_NTPC']=pd.DataFrame(np.where(data_no_missing['Price_NTPC']
                                          < ll_Price_NTPC, np.nan, 
                          np.where(data_no_missing['Price_NTPC'] > 
                                   ul_Price_NTPC, np.nan,
                                   data_no_missing['Price_NTPC']))) 
          
          data_no_missing['Price_Shenhua']=pd.DataFrame(np.where(data_no_missing['Price_Shenhua']
                                          < ll_Price_Shenhua, np.nan, 
                          np.where(data_no_missing['Price_Shenhua'] > 
                                   ul_Price_Shenhua, np.nan,
                                   data_no_missing['Price_Shenhua']))) 
          
          data_no_missing['tempmax_RB']=pd.DataFrame(np.where(data_no_missing['tempmax_RB']
                                          < ll_tempmax_RB, np.nan, 
                          np.where(data_no_missing['tempmax_RB'] > 
                                   ul_tempmax_RB, np.nan,
                                   data_no_missing['tempmax_RB']))) 
          
          data_no_missing['tempmin_RB']=pd.DataFrame(np.where(data_no_missing['tempmin_RB']
                                          < ll_tempmin_RB, np.nan, 
                          np.where(data_no_missing['tempmin_RB'] > 
                                   ul_tempmin_RB, np.nan,
                                   data_no_missing['tempmin_RB']))) 
          
          data_no_missing['temp_RB']=pd.DataFrame(np.where(data_no_missing['temp_RB']
                                          < ll_temp_RB, np.nan, 
                          np.where(data_no_missing['temp_RB'] > 
                                   ul_temp_RB, np.nan,
                                   data_no_missing['temp_RB']))) 
          
          data_no_missing['dew_RB']=pd.DataFrame(np.where(data_no_missing['dew_RB']
                                          < ll_dew_RB, np.nan, 
                          np.where(data_no_missing['dew_RB'] > 
                                   ul_dew_RB, np.nan,
                                   data_no_missing['dew_RB']))) 
          
          data_no_missing['humidity_RB']=pd.DataFrame(np.where(data_no_missing['humidity_RB']
                                          < ll_humidity_RB, np.nan, 
                          np.where(data_no_missing['humidity_RB'] > 
                                   ul_humidity_RB, np.nan,
                                   data_no_missing['humidity_RB']))) 
          
          data_no_missing['precip_RB']=pd.DataFrame(np.where(data_no_missing['precip_RB']
                                          < ll_precip_RB, np.nan, 
                          np.where(data_no_missing['precip_RB'] > 
                                   ul_precip_RB, np.nan,
                                   data_no_missing['precip_RB']))) 
          
          data_no_missing['windspeed_RB']=pd.DataFrame(np.where(data_no_missing['windspeed_RB']
                                          < ll_windspeed_RB, np.nan, 
                          np.where(data_no_missing['windspeed_RB'] > 
                                   ul_windspeed_RB, np.nan,
                                   data_no_missing['windspeed_RB']))) 
          
          data_no_missing['tempmax_Limpopo']=pd.DataFrame(np.where(data_no_missing['tempmax_Limpopo']
                                          < ll_tempmax_Limpopo, np.nan, 
                          np.where(data_no_missing['tempmax_Limpopo'] > 
                                   ul_tempmax_Limpopo, np.nan,
                                   data_no_missing['tempmax_Limpopo']))) 
          
          data_no_missing['tempmin_Limpopo']=pd.DataFrame(np.where(data_no_missing['tempmin_Limpopo']
                                          < ll_tempmin_Limpopo, np.nan, 
                          np.where(data_no_missing['tempmin_Limpopo'] > 
                                   ul_tempmin_Limpopo, np.nan,
                                   data_no_missing['tempmin_Limpopo']))) 
          
          data_no_missing['temp_Limpopo']=pd.DataFrame(np.where(data_no_missing['temp_Limpopo']
                                          < ll_temp_Limpopo, np.nan, 
                          np.where(data_no_missing['temp_Limpopo'] > 
                                   ul_temp_Limpopo, np.nan,
                                   data_no_missing['temp_Limpopo']))) 
          
          data_no_missing['dew_Limpopo']=pd.DataFrame(np.where(data_no_missing['dew_Limpopo']
                                          < ll_dew_Limpopo, np.nan, 
                          np.where(data_no_missing['dew_Limpopo'] > 
                                   ul_dew_Limpopo, np.nan,
                                   data_no_missing['dew_Limpopo']))) 
          
          data_no_missing['humidity_Limpopo']=pd.DataFrame(np.where(data_no_missing['humidity_Limpopo']
                                          < ll_humidity_Limpopo, np.nan, 
                          np.where(data_no_missing['humidity_Limpopo'] > 
                                   ul_humidity_Limpopo, np.nan,
                                   data_no_missing['humidity_Limpopo']))) 
          
          data_no_missing['precip_Limpopo']=pd.DataFrame(np.where(data_no_missing['precip_Limpopo']
                                          < ll_precip_Limpopo, np.nan, 
                          np.where(data_no_missing['precip_Limpopo'] > 
                                   ul_precip_Limpopo, np.nan,
                                   data_no_missing['precip_Limpopo']))) 
          
          data_no_missing['windspeed_Limpopo']=pd.DataFrame(np.where(data_no_missing['windspeed_Limpopo']
                                          < ll_windspeed_Limpopo, np.nan, 
                          np.where(data_no_missing['windspeed_Limpopo'] > 
                                   ul_windspeed_Limpopo, np.nan,
                                   data_no_missing['windspeed_Limpopo']))) 
          
          ### LINEAR INTERPOLATION OF NAN VALUES
          
          inter_data=data_no_missing[['Coal_RB_4800_FOB_London_Close_USD',
                 'Coal_RB_5500_FOB_London_Close_USD',
                 'Coal_RB_5700_FOB_London_Close_USD',
                 'Coal_RB_6000_FOB_CurrentWeek_Avg_USD',
                 'Coal_India_5500_CFR_London_Close_USD', 'Price_WTI', 'Price_Brent_Oil',
                 'Price_Dubai_Brent_Oil', 'Price_ExxonMobil', 'Price_NTPC',
                 'Price_Shenhua', 'tempmax_RB', 'tempmin_RB', 'temp_RB', 'dew_RB',
                 'humidity_RB', 'precip_RB', 'windspeed_RB', 'tempmax_Limpopo',
                 'tempmin_Limpopo', 'temp_Limpopo', 'dew_Limpopo', 'humidity_Limpopo',
                 'precip_Limpopo', 'windspeed_Limpopo']].interpolate(method='linear',limit_direction='both',axis=1)
          
          ### COMBINING THE INTERPOLATED DATA WITH OTHER CATEGORICAL COLUMNS
          data_interpolated=pd.concat([data_no_missing['Date'], inter_data, data_no_missing[['preciptype_RB','preciptype_Limpopo']]],axis=1)
          

    ```

3. Perform data exploration and visualization:
    ```python
      ### Vizualization ###
      ## The code shows the few sample visuals/charts of the features in the dataset ##
      import matplotlib.pyplot as plt
      import seaborn as sns
      import scipy.stats as stats
      import pylab
      import numpy as np
      
      sns.histplot(data['Coal_RB_4800_FOB_London_Close_USD'],kde=True)
      plt.title('Before Pre-processing')
      plt.xlabel('Coal_RB_4800_FOB_London_Close_USD')
      plt.ylabel('Frequency')
      plt.show()
      
      sns.histplot(np.log(data_no_missing['Coal_RB_4800_FOB_London_Close_USD']),kde=True)
      plt.title('After Pre-processing')
      plt.xlabel('Coal_RB_4800_FOB_London_Close_USD')
      plt.ylabel('Frequency')
      plt.show()
      
      sns.histplot(data['Coal_RB_5500_FOB_London_Close_USD'],kde=True)
      plt.title('Before Pre-Processing')
      plt.xlabel('Coal_RB_5500_FOB_London_Close_USD')
      plt.ylabel('Frequency')
      plt.show()
      
      sns.histplot(np.log(data_no_missing['Coal_RB_5700_FOB_London_Close_USD']),kde=True)
      plt.title('After Pre-processing')
      plt.xlabel('Coal_RB_5700_FOB_London_Close_USD')
      plt.ylabel('Frequency')
      plt.show()
      
      sns.histplot(data['Coal_RB_6000_FOB_CurrentWeek_Avg_USD'],kde=True)
      plt.title('Histogram for Coal_RB_6000_FOB_CurrentWeek_Avg_USD')
      plt.xlabel('Coal_RB_6000_FOB_CurrentWeek_Avg_USD')
      plt.ylabel('Frequency')
      plt.show()
      
      sns.histplot(data['Coal_India_5500_CFR_London_Close_USD'],kde=True)
      plt.title('Histogram for Coal_India_5500_CFR_London_Close_USD')
      plt.xlabel('Coal_India_5500_CFR_London_Close_USD')
      plt.ylabel('Frequency')
      plt.show()
      
      stats.probplot(data['Coal_RB_4800_FOB_London_Close_USD'],dist='norm',plot=pylab)
      plt.title('Before Outlier Treatment')
      plt.show()
      
      stats.probplot(data_interpolated['Coal_RB_4800_FOB_London_Close_USD'],dist='norm',plot=pylab)
      plt.title('After Outlier Treatment')
      plt.show()
      
      stats.probplot(data['Coal_RB_5500_FOB_London_Close_USD'],dist='norm',plot=pylab)
      plt.title('Before Outlier Treatment')
      plt.show()
      
      stats.probplot(data_interpolated['Coal_RB_5500_FOB_London_Close_USD'],dist='norm',plot=pylab)
      plt.title('After Outlier Treatment')
      plt.show()
    ```

<img src="https://github.com/NaveenM-10/Coal-Price-Analysis/blob/main/Coal%20Price%20Analysis/Screenshot%202024-09-10%20173528.png" width="300" height="300"/>  <img src="https://github.com/NaveenM-10/Coal-Price-Analysis/blob/main/Coal%20Price%20Analysis/Screenshot%202024-09-10%20173721.png" width="300" height="300"/>

<img src="https://github.com/NaveenM-10/Coal-Price-Analysis/blob/main/Coal%20Price%20Analysis/Screenshot%202024-09-10%20175204.png" width="300" height="300"/>  <img src="https://github.com/NaveenM-10/Coal-Price-Analysis/blob/main/Coal%20Price%20Analysis/Screenshot%202024-09-10%20175217.png" width="300" height="300"/>

<img src="https://github.com/NaveenM-10/Coal-Price-Analysis/blob/main/Coal%20Price%20Analysis/Screenshot%202024-09-10%20181512.png" width="300" height="300"/>  <img src="https://github.com/NaveenM-10/Coal-Price-Analysis/blob/main/Coal%20Price%20Analysis/Screenshot%202024-09-10%20181559.png" width="300" height="300"/>

<img src="https://github.com/NaveenM-10/Coal-Price-Analysis/blob/main/Coal%20Price%20Analysis/Screenshot%202024-09-10%20181512.png" width="300" height="300"/>  <img src="https://github.com/NaveenM-10/Coal-Price-Analysis/blob/main/Coal%20Price%20Analysis/Screenshot%202024-09-10%20181803.png" width="300" height="300"/>

## Interactive dashboards:
    Open the Power BI / Looker Studio / Excel files in the `dashboards` directory to view the interactive visualizations.
# Power BI Dashboard
<img src="https://github.com/NaveenM-10/Coal-Price-Analysis/blob/main/Coal%20Price%20Analysis/Screenshot%202024-09-10%20181928.png"/>

<img src="https://github.com/NaveenM-10/Coal-Price-Analysis/blob/main/Coal%20Price%20Analysis/Screenshot%202024-09-10%20181944.png"/>


# Looker Studio Dashboard
<img src="https://github.com/NaveenM-10/Coal-Price-Analysis/blob/main/Coal%20Price%20Analysis/Screenshot%202024-09-10%20182032.png"/>

<img src="https://github.com/NaveenM-10/Coal-Price-Analysis/blob/main/Coal%20Price%20Analysis/Screenshot%202024-09-10%20182042.png"/>


## Project Structure
Coal-Price-Analysis/
- data/
  - raw_data.csv
- scripts/
  - data_cleaning.py
  - data_analysis.py
- dashboards/
  - dashboard.pbix
  - dashboard.looker
  
- images/
  - [charts.png](https://github.com/NaveenM-10/Coal-Price-Analysis/tree/main/Coal%20Price%20Analysis)
  - [README.md](https://github.com/NaveenM-10/Coal-Price-Analysis/blob/main/README.md)
