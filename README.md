# Medical-Data-Visualizer

In this project, the objective is to visualize and make calculations from medical examination data using <kbd>matplotlib</kbd>, <kbd>seaborn</kbd>, and <kbd>pandas</kbd>. The dataset values provided for this project were collected during medical examinations.

## Table of Content
* [Introduction](#intro)
* [Tasks](#task)
* [Solution Breakdown](#sol)
  * [Categorical Plot](#cat)
  * [Heat PLot](#heat)

## Intorduction <a name="intro"></a>

The rows in the dataset represent patients and the columns represent information such as body measurements, results from various blood tests, and lifestyle choices. The objective is to use the dataset to explore the relationship between cardiac disease, body measurements, blood markers, and lifestyle choices.

File name: [medical_examination.csv](medical_examination.csv).

| Feature | Variable Type | Variable | Value Type |
|---|---|---|---|
| Age | Objective Feature | age | int (days) |
| Height | Objective Feature | height | int (cm) |
| Weight | Objective Feature | weight | float (kg) |
| Gender | Objective Feature | gender | category code |
| Systolic blood pressure | Examination Feature | ap_hi | int |
| Diastolic blood pressure | Examination Feature | ap_lo | int |
| Cholesterol | Examination Feature | cholesterol | 1: normal, 2: above normal, 3: well above normal |
| Glucose | Examination Feature | gluc | 1: normal, 2: above normal, 3: well above normal |
| Smoking | Subjective Feature | smoke | binary |
| Alchol intake | Subjective Feature | alco | binary |
| Physical activity | Subjective Feature | active | binary |
| Presence or absence of cardiovascular disease | Target Variable | cardio | binary |

## Tasks <a name="task"></a>

Create a chart similar to [examples/Figure_1.png](https://github.com/abarriebee/Medical-Data-Visualizer/blob/091ebc90d12d61f7a648208ea501b0b7e5f645f0/examples/Figure_1%20(1).png), where we show the counts of good and bad outcomes for the <kbd>cholesterol</kbd>, <kbd>gluc</kbd>, <kbd>alco</kbd>, <kbd>active</kbd>, and <kbd>smoke</kbd> variables for patients with cardio=1 and cardio=0 in different panels.

Use the data to complete the following tasks in [medical_data_visualizer.py](medical_data_visualizer.py):

* Add an <kbd>overweight</kbd> column to the data. To determine if a person is overweight, first calculate their BMI by dividing their weight in kilograms by the square of their height in meters. If that value is > 25 then the person is overweight. Use the value 0 for NOT overweight and the value 1 for overweight.
* Normalize the data by making 0 always good and 1 always bad. If the value of <kbd>cholesterol</kbd> or <kbd>gluc</kbd> is 1, make the value 0. If the value is more than 1, make the value 1.
* Convert the data into long format and create a chart that shows the value counts of the categorical features using seaborn's <kbd>catplot()</kbd>. The dataset should be split by 'Cardio' so there is one chart for each <kbd>cardio</kbd> value. The chart should look like [examples/Figure_1.png](https://github.com/abarriebee/Medical-Data-Visualizer/blob/091ebc90d12d61f7a648208ea501b0b7e5f645f0/examples/Figure_1%20(1).png).
* Clean the data. Filter out the following patient segments that represent incorrect data:
  * diastolic pressure is higher than systolic (Keep the correct data with <kbd>(df['ap_lo'] <= df['ap_hi'])</kbd>)
  * height is less than the 2.5th percentile (Keep the correct data with <kbd>(df['height'] >= df['height'].quantile(0.025))</kbd>)
  * height is more than the 97.5th percentile
  * weight is less than the 2.5th percentile
  * weight is more than the 97.5th percentile
* Create a correlation matrix using the dataset. Plot the correlation matrix using seaborn's <kbd>heatmap()</kbd>. Mask the upper triangle. The chart should look like [examples/Figure_2.png](https://github.com/abarriebee/Medical-Data-Visualizer/blob/091ebc90d12d61f7a648208ea501b0b7e5f645f0/examples/Figure_2%20(1).png).

Any time a variable is set to <kbd>None</kbd>, we must make sure to set it to the correct code.

Unit tests are written for you under [test_module.py](test_module.py).
 
 ## Solution Breakdown <a name="sol"></a>
 
 We can begin our code by importing the data from the [CSV file](medical_examination.csv) given to us:
 
 ```
 df = pd.read_csv("medical_examination.csv")
 ```
 ### Categorical Plot <a name="cat"> </a>
 
 1. In the following code listed below, we are adding an overweight column as instructed, to determine if a person is overwight by calculating their BMI. When considering the BMI, we must consider how the height given is in cm. Therefore, we must divide the height by 100 to convert into meters. An addition, we apply a lambda function to dictate if the person's BMI value, labda x,  exceeds <kbd> > 25 </kbd>. If the lambda x value is <kbd> > 25 </kbd> then function will return a 1 to rule that the person is overweight, otherwise, a 0 is returned.
 
 ```
 df['overweight'] = (df["weight"] / (df["height"] / 100) ** 2).apply(lambda x : 1 if x > 25 else 0)
 ```
 
2. Normalizing data by making the return value, 0 ,good and 1 bad. Therefore, if the value of 'cholesterol' or 'gluc' is good, at a level 1 out of 3, that value would be considered as a 0. However, if the value exceeds level 1, above the average and well above average, then that value will be considered bad and have value of 1.
 ```
df["cholesterol"] = df["cholesterol"].apply(lambda x : 0 if x == 1 else 1)
df["gluc"] = df["gluc"].apply(lambda x : 0 if x == 1 else 1)
 ```
3. Drawing categorical plot and creating a DataFrame for the cat plot using `pd.melt` while solely using the values from 'cholesterol', 'gluc', 'smoke', 'alco', 'active', and 'overweight':
```
def draw_cat_plot():
 
 df_cat = pd.melt(df, id_vars = ["cardio"], value_vars = ['cholesterol', 'gluc', 'smoke', 'alco', 'active', 'overweight'])

```
4. Here, we must group and reformat the data in order to split the 'cardio' dataset to show the counts of each feature. Then, in 4.1, we must rename one of the columns for the <kbd>catplot()>/kbd> to function properly:
 ```
    df_cat["total"] = 1
    df_cat = df_cat.groupby(["cardio", "variable", "value"], as_index = False).count()
 ```
 
 4.1. Draw the seaborn catplot with 'sns.catplot()':
 ```
    sns.set_theme(style = "darkgrid")

    fig = sns.catplot(x = "variable", y = "total", data = df_cat, hue = "value", kind = "bar", col = "cardio").fig
 ```
 
 Saving and returning a catplot:
 ```
    fig.savefig('catplot.png')
    return fig
 ```
 
<details>
   <summary>
    Return figure of categorized plot:
    
  </summary>
  
  ![image](https://github.com/abarriebee/Medical-Data-Visualizer/blob/881775a322c6bd5ae7fabd6c531e15df7a673932/Solutions/catplot.png)
  
 </details>
 
 ### Heat Plot <a name="heat"></a>
 
 We begin by defining the heat map:
 ```
 def draw_heat_map():
 ```
 5. Now, we must clean the data to gather the desired range of height and weight.
 ```
    df_heat = df[
      (df['ap_lo'] <= df['ap_hi']) &
      (df['height'] >= df ['height'].quantile(0.025)) &
      (df['height'] <= df ['height'].quantile(0.975)) &
      (df['weight'] >= df ['weight'].quantile(0.025)) &
      (df['weight'] <= df ['weight'].quantile(0.975))]
```
6. Next, we calculate the correlation matrix:
```
corr = df_heat.corr(method="pearson")                        
```
6.1. Generating the mask for the upper triangle:
```
mask = np.triu(corr)
```
  
Setting up the matplotlib figure:
```  
fig, ax = plt.subplots(figsize = (12,12))                        
```
6.2. Seaborn heat map:
```
sns.heatmap(corr, linewidths=1, annot = True, square = True, mask = mask, fmt = ".1f", center = 0.08, cbar_kws = {"shrink": 0.5})
                        
```     
                        
Saving and returning heatmap:
```
fig.savefig('heatmap.png')
return fig                        
```                        
<details>
  <summary>
    Return figure of heat map:
  </summary>
 
 ![image](https://github.com/abarriebee/Medical-Data-Visualizer/blob/881775a322c6bd5ae7fabd6c531e15df7a673932/Solutions/heatmap.png)
 
 </details>
