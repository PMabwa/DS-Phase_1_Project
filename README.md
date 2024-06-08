# Pier & Co Services Ltd Aviation Analysis Project.
# Objectives:
1) Evaluate the correlation between the features within the dataset. 
2) Establish the current trends in fatal accidents between the period (2000 - 2022).
3) Determine which aircraft Pier & Co Services Ltd should invest in while they venture into the air transport business.
# Summmary

Pier & Co Service Ltd is seeking to venture into the aviation industry, however, the company seeks insights that will inform their decisions about their new venture. 

The following data analysis process will include the use of python libraries including pandas, numpy, matplotlib and seaborn, aiming to provide accurate information as to how specific factors contribute to aviation accidents. The factors to be analyzed are limited to the features provided in the  AviationData.csv dataset. 
# Definition of aviation term:

FAR.Description (Federal Aviation Regulations)- According to https://www.lawinsider.com/dictionary/federal-aviation-regulation-far , FAR is the body of rules prescribed by the Federal Aviation Authority(FAA) governing all aviation activities in the United States. Therefore, the applicable description(parts of the rules) is dependent on the type of aircraft.
# Data cleaning and Analysis Process

First pick the columns to be featured and analyzed 
select_df = df[["Event.Date","Location", "Country", "Weather.Condition","Aircraft.damage", "Aircraft.Category", "Make", "Model", "Engine.Type", "Number.of.Engines", "Injury.Severity", "Total.Fatal.Injuries", "Total.Serious.Injuries", "Total.Minor.Injuries", "Total.Uninjured", "FAR.Description", "Broad.phase.of.flight", "Purpose.of.flight"]]

select_df.head()

select_df.info()
# First Objective:
Demonstrate the correlation between the number of accidents and the following features; Aircraft.Damage, Injury.Severity, Engine.Type, Number of Engines, FAR.Description, Purpose.of.Flight, "Weather.Condition, Make , Broad.phase.of.flight and Aircraft.Category. 

Histogram charts easily demonstrate this.
#drawing the histograms
for feature in hist_num:
    plt.figure(figsize=(10, 6))
    select_df[feature].plot(kind='hist', bins=30, title=f'Histogram of {feature}')
    plt.xlabel(feature)
    plt.ylabel('Frequency')
    plt.show()

# Plot bar plots for categorical features
for feature in hist_obj:
    plt.figure(figsize=(10, 6))
    select_df[feature].value_counts().plot(kind='bar', title=f'Bar Plot of {feature}')
    plt.xlabel(feature)
    plt.ylabel('Frequency')
    plt.show()

    Observations

From the charts above comparing the frequency of accidents to each respective selected feature between 1948 - 2022, the following are the derived observations ;
1) Aircrafts with just 1 engine have higher numbers of accidents in comparison to those with 2,3 & 4 number of engines.
2) The frequency of accidents in relation to the purpose of flight, it can be observed that the personal and instructional bins have significant numbers in comparison to the others.
3) The FAR.Description chart, demonstrates that 91 & part 091 (civil aircrafts) are majorly affected when it comes to accidents.
4) Referencing the last 2 charts, it is observable that most of the accidents occur during landing and the highest category affected are airplanes followed by helicopters.
5) One outstanding observation is that many accidents occur during VMC which translates to good weather patterns in accordance with visual flight rules.

# Second Objective:
To get a more current and accurate analysis, filtering the dataset to reflect recent data collected between the year 2000 - 2022
# Extract the year from the date format in the dataset 
select_df["Event.Date"] = pd.to_datetime(select_df["Event.Date"])

select_df.loc[:, "year"] = select_df["Event.Date"].dt.year

# To avoid errors transform the year column to an integer format

select_df["year"] = select_df["year"].astype(int)

# Filter the dataframe to the years 2000 -2022

selected_df = select_df[(select_df["year"] >= 2000) & (select_df["year"]<= 2022)]

# Confirming the change has been effected

selected_df.info()

Given the presence of missing values in the selected dataset, choosing to fill the null values with ("unknown") is a good strategy as it does not disrupt the dataset and avoid undue bias.

# select the columns to be filled with "unknown"
columns_to_fill = [
    "Aircraft.Category", "Make", "Location", "Weather.Condition",
    "Country", "Broad.phase.of.flight", "Aircraft.damage",
    "FAR.Description", "Purpose.of.flight", "Model"
]

# Fill the selected columns
selected_df[columns_to_fill] = selected_df[columns_to_fill].fillna("unknown")

selected_df.head()

selected_df.head()

# Third Objective
Analyzing the selected data

First, get the trajectory of fatalities recorded within the selected period (2000 - 2022) to understand the trend and how risky the sector is currently. 

# create a fatalities variable
fatalities = selected_df[selected_df["Total.Fatal.Injuries"] > 0]

# group the fatalities by year 

fatalities_by_year = fatalities.groupby("year").size()

# plot the trend over the years 

plt.figure(figsize=(10,6))
fatalities_by_year.plot(kind="line", marker = "o")
plt.xlabel("year")
plt.ylabel("Number of Fatalities")
plt.title("Trend of fatalities between the year 2000 - 2022")
plt.grid = True
plt.show()

Observation

The trend of aircraft accidents that occured in the period (2000 - 2022) has continously decreased. This is probably due to improvement in aircraft technology and well trained pilots. 

Observing that there is a similarity in names of the make of the aircraft and the way to differentiate them is by their model category, combining the two respective columns solves this issue. First, convert all 'Make' names to lower strings (to enhance consistency in the column), then bring together the "Make" and "Model" columns into one. 

# convert all names to lowercase
selected_df["Make"] = selected_df["Make"].str.lower()

# combine and create a new column "Make_Model"
selected_df ["Make_Model"] = selected_df["Make"] + " " + selected_df["Model"]

selected_df = selected_df.drop(columns=["Make", "Model"])

selected_df

Group the relevant columns (Make_Model and Aircraft.Category) , removing the unknown values and plot a bar chart to show the top 10 models appearing in accident report. 

# group the two separate columns together
makevs_cart = selected_df[selected_df["Aircraft.Category"]!= "unknown"].groupby(["Make_Model", "Aircraft.Category"]).size().reset_index(name="AccidentCount")

# sort in descending order (highest to lowest) and show the first 10

top10_makevs_cart = makevs_cart.sort_values(by="AccidentCount", ascending=False).head(10)

top10_makevs_cart

# Plot the data using a bar chart
plt.figure(figsize=(12, 8))
bar_plot = plt.barh(top10_makevs_cart["Make_Model"] + ' - ' + top10_makevs_cart["Aircraft.Category"], top10_makevs_cart["AccidentCount"])
plt.xlabel('Number of Accidents')
plt.ylabel("Make_Model")
plt.title('Top 10 Make_Model & Aircraft.Category Combinations by Number of Accidents')
plt.gca().invert_yaxis()  

# y-axis inverts to display the highest value at the top
plt.show()

Obsevation

Based on the chart above, it is evident that the number of cessna aircrafts reported is high. Despite this significant figures, simpleflying.com describes the cessna model as the most popular among beginner pilots due to the significant low fatalities reported. Therefore, the high number of reported accidents demonstrates how popular the aircraft is within the sector. 

Recommendation

Pier & Co Services Ltd should consider incorporating the cessna aircraft model among its fleet before expanding into the large commercial aircraft. 

# Reviewing the safety of the make and model of air crafts.

The above chart has displayed the most popular aircraft make and model in accordance to the data set. Therefore, to understand the safety of these models, plotting a chart comparing the total fatalities to the respective make provides a clearer picture of an aircraft's model suitability.

fatalities_group = selected_df.groupby('Make_Model')["Total.Fatal.Injuries"].sum().reset_index()

top10fatal_models = fatalities_group.sort_values(by="Total.Fatal.Injuries", ascending=False).head(10)

top10fatal_models
plt.figure(figsize=(12, 8))
plt.bar(top10fatal_models['Make_Model'], top10fatal_models["Total.Fatal.Injuries"], color='skyblue')
plt.xlabel('Aircraft Make and Model')
plt.ylabel('Total Fatal Injuries')
plt.title('Top 10 Aircraft Models with Fatal Injuries')
plt.xticks(rotation=45, ha='right')
plt.tight_layout()
plt.show()

Observation

The bart chart above provides a summary of the top models with fatal injuries. The highest being boeing and airbus models. This explains the why most aviation stakeholders prefer the cessna models as it has better safety standards in comparison to most aircrafts. 

Recommendation

Pier & Co Services Ltd should consider the safety standards as well when acquiring a new fleet for their aircraft business. 

# Type of Engine

Engines are an imporatn aspect in airplanes and to understand which one suits the need, extracting the top five aircraft engines from the dataset proves to be an effective approach.
top5_engines = selected_df["Engine.Type"].value_counts().head(5)
top5_engines

# Plot pie chart
plt.figure(figsize=(10, 7))
plt.pie(top5_engines, labels=top5_engines.index, autopct='%1.1f%%', colors=plt.cm.Paired.colors)
plt.title('Top 5 Types of Engines by Count')
plt.show()

Observation 

The type of engine with the highest number of accidents reported is shown figure above to be the "Reciprocating" engine while the "Turbo jet" has the least frequency. This translates to its popularity among aviation enthusiasts. According to chapter 7 of the Federal Aviation Administration(FAA), most small aircrafts are designed with the reciprocating engine. Furthermore, the simplicity, reliability and low fuel cost factor in to how prominent this engine among small aircraft owners/airlines.  

Recommendation

As a new company, Pier and Co. Services Ltd need to consider starting out with smaller aircrafts before acquiring bigger planes with expensive maintainance costs, especially the engine. 

# Phase of Accident

Aircraft accident occur during different phases, hence the need to know the high risk phases of an aircraft' journey. 

# use value_counts method to extract the frequently reported phases of accidents
selected_df["Broad.phase.of.flight"].value_counts()

# create a new variable and group together both columns 
# drop unknown 
phase_by_con = selected_df[selected_df["Broad.phase.of.flight"]!= "unknown"].groupby(["Broad.phase.of.flight", "Weather.Condition"]).size().unstack().fillna(0)

# plot the heatmap to demonstrate the correlation between the columns
phase_by_con.plot(kind='bar', stacked=True, figsize=(10, 7))
plt.xlabel('Broad Phase of Flight')
plt.ylabel('Number of Occurrences')
plt.title('Correlation between Broad Phase of Flight and Weather Conditions')
plt.legend(title='Weather Condition')
plt.show()


Observation

With reference to the stacked bar chart above, majority of accidents occur during landing phase and interestingly while the weather conditions are favorable (VMC). The second highest occurence is observed when air crafts are taking off within similar weather conditions. 

According to IATA 2022 Annual Safety Report, aircraft accidents taking place during landing and takeoff phases are attributed to overrun(aircraft continuing beyond the runway ) or veering out of its lateral limits.

Recommendations

Provide scenario-based training to pilots, enhancing their competencies for effective threat and management to prevent runway excursion (e.g., contaminated runway, last minute change of runway, deterioration of weather condition).

Airlines and private plane owners should explore advanced technologies such as AI-based systems which can aid aircrew in obtaining
information, and making rapid decisions.

For Airlines and Air Traffic control, they should consider recommending the execution of a go-around at any point during the approach, when there is any doubt on a safe continuation of the approach or the landing. 

