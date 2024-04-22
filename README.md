# Pattern-Analysis-Framework-for-cryptocurrency

I've been working on this project for some time now, and it seems to be doing great, so I've decided to share it. Every couple of days, I add some new features to it, and I now consider it a great tool for conducting exciting analyses on any cryptocurrency's historical data and discovering interesting patterns hidden within.

**Pattern Analysis Framework for Crypto:** This project is a Python framework that enables users to create valuable analyses easily, relying on pattern recognition and clustering data mining methods.

**How it works:**

Deep inside this code, the main idea is to find patterns in the data and study the special behavior for every extracted pattern.

1. **Data Cleaning:** If the data is historical cryptocurrency data, we skip this part because the data is already cleaned.

2. **Data Simplification:** The user chooses a window size, let's say 24, and a step size, let's say 5. This means the records will be divided into parts, with each part containing 24 records, and the space between the last point in one group and the first point in the next group is 5. After this division, the real simplification happens using the PIPS method to convert the 24 records to 5 records or more, as the user decides. These 5 points must represent the most important points in the group and the points that represent the real movement and pattern inside the group.

3. **Normalization of Records:** The records are in different scales representing prices, so the price is converted to the percentage of change. Then, the x-axis containing time is also converted into the percentage of change. Next, we calculate the rate of change of the price over time to end with one number representing each point.

4. **Clustering Data:** The next step is to cluster the data into clusters of points that look more like each other than the rest. However, the challenge is deciding the right number of clusters in the data. The user inputs a range for the number of patterns and a range of variations for every pattern. Using the Silhouette score, the code decides the best number of patterns to cluster the data into. Then, clustering with the K-means algorithm occurs. After the first clustering, every cluster enters the next step of clustering to extract different variations inside every cluster, and again, the Silhouette score is calculated to decide the best number for clustering. If you plot the clusters, you'll see how every cluster is unique and has a specific shape. If you plot the variations, you'll see how inside every cluster, there are a lot of variations. Sometimes, the same shape can end, for example, with an uptrend or downtrend while it has the same unique shape and characteristics. After the clustering phase, we move to the study phase.

5. **Analysis Phase:** After the clustering phase, we move to the study phase. The user inputs the point of measure (POM), let's say 48. This means that after building the KD tree for pattern detection, we loop over the whole data, and whenever we find a specific pattern, we move ahead by 48 records and measure the change that happened to the price to detect the effect of this pattern on the price. We also log other things like the change of the price inside the pattern itself and so on. Then, we conduct our analysis to determine what patterns have what characteristics and what effect.

6. **Pattern Classification:** The user inputs a threshold and an average threshold to divide the patterns according to it. For example, the patterns that have an uptrend after it with a percentage of 70% and an average uptrend volume of 1%. According to this, we extract two groups that match the user criteria: the uptrend group and the downtrend group.

7.  **Project Evolution:**
The project continuously improves with frequent updates, introducing advanced features like double clustering, variation identification within patterns, sequence saving for future predictive analysis, and reduced training time, and more 


**How to use it:**

Simply, you can create a clustering analysis instance, then give it all the parameters you need. After this, this instance has all the functionalities I talked about and a lot more. You can use `.train()` to train it on your data. You can use `.plot_behavior()` to plot the groups' appearance pie chart. You can use `.get_signals()` and give it new records, and the return will be a buy signal, sell signal, or hold signal according to the train and so on. The notebook contains a small documentation containing all the methods you can use to complete your analysis, then save it in a special file to load it and use or continue the work on it later.

# documentaion:

### creation of instance:

```
#first you need to your analysis object instance

#the name of the object
name = "sol_analysis" 
#the historical data of the currency
data_file = "sol.csv"
#the number of point that will be in every pattern after simplification (you can fine tune it, defult = 5)
row_points = 5
#the window size for each pattern before simplification (you can fine tune it, defult = 24)
window_size = 24
#the steps to skip between every pattern and the next one (you can fine tune it, defult = 10)
step_size = 5
#the object will detect the most suitable number of cluster inside the range you give here (you can fine tune it, defult = (25,45))
range_main = range(25,40)
#for every pattern the object will find internal variations inside it , this is the range of vaiation for every pattern and the object will change the range dynamicly and find the most suitable number of clusters insde the range
range_secondary = range(10,20)
#if the percentage of possitve trend after some pattern is greater than the threshold it will considerd up trend pattern and the opposite for down trend
threshold = 70
#the minimum average of up trend sizes of the clusters for up trend clusters and with negative for down trend ones
min_average = 1
#the point of measuring the change after the cluster detecter (length of trade)
pom = 10

#creating the instance
sol_analysis = cluster_analysis(name,data_file,row_points,window_size,step_size,range_main,range_secondary,threshold,min_average,pom) 
```

### main methods:

```
#main functionalities :

#train the system
sol_analysis.train()

#if you stoped the training process in the backtesting phase (because it might need sometime to complete) you can continue from the points you stopped in
sol_analysis.continue_future_test()

#to plot the discovered patterns after training
sol_analysis.plot_patterns()

#to save patterns as images in plots folder
sol_analysis.save_patterns_plots()

#to plot the pie chart that represent the percentage of up trend patterns to down trend patterns to mixed trend patterns in the patterns discovered from the historical data
#it is important to predict the trading frequency depend on this analysis
sol_analysis.plot_behavior()

#to save the instance to load it later and dont have to start train it again
sol_analysis.save()

#to delete unwanted files after training and saving the instance you might need to delete unwanted files that created due to training
sol_analysis.clean_files()

#to get signal depend on the result of the analysis simply follow the this example
window = [
    107.57, 107.42, 107.76, 107.75, 107.42, 108.16, 108.09, 108.42, 108.85,
    109.39, 108.87, 108.58, 108.31, 108.07, 107.99, 107.72, 107.39, 107.96,
    107.69, 107.33, 107.1, 106.85, 106.65, 107.02
]
signal , cluster = sol_analysis.get_signals(window)

#get the analysis of certain cluster
info = sol_analysis.get_info(cluster)
#max up trend
max = float(info["Max Change in Next Data"].values[0])
#max down trend
min = float(info["Min Change in Next Data"].values[0])


#to know the future possiblities you should this function , depend on an internal fb tree inside the instance it should tells you the patterns that most likely to happen after the current one and what is its behaviors and the percentage of each of them to be the next one
possible_clusters = sol_analysis.next_cluster(cluster)
print(possible_clusters)

#to load any saved pretrained instances and work with it directly
sol_analysis.load("sol_analysis.pkl")


#backtest data
data_test = "sol test.csv"
#to backtest the strategy based on the result
sol_analysis.backtest(data_test)


#to change the average and threshold after training 
new_threshold = 60
new_average = 0.5

sol_analysis.set_new(new_average , new_threshold)

```

### additional methods :

```
#additional functionalities :

#to save only the finder object that can detect patterns without any other data
sol_analysis.save_cluster_finder()

#backup analysis files like analysis csvs that secure trianing process
sol_analysis.backup()


#to display the fb tree that represent the sequence of patterns that happen after each other
sol_analysis.display_tree()


#to save the fb tree
sol_analysis.save_tree()


#to load saved fb tree
sol_analysis.load_tree()


#to load saved finder file
sol_analysis.load_cluster_finder("sol_analysis_cluster_finder.pkl")

```

### backtest :

```

from backtesting import Backtest, Strategy
from backtesting.lib import crossover


data_test = "TEST.csv" #  must be a pandas.DataFrame with columns 'Open', 'High', 'Low', 'Close', and (optionally) 'Volume'


class str(Strategy):

    window_len = 24

    def init(self):
        close = self.data.Close


    def next(self):

        #signal
        if len(self.data) >= self.window_len:
          window = np.array(self.data.Close[-self.window_len:])
          signal , cluster = sol_analysis.get_signals(window)

          if "buy" in signal:
              self.buy()
          elif "sell" in signal:
              self.sell()





data = pd.read_csv(data_test)

bt = Backtest(data, str,
              cash=1000, commission=.002,
              exclusive_orders=True)

output = bt.run()
bt.plot()
print(output)

```


# media :

### example of exteracted patterns :


    
![IMAGE 2024-04-21 19:39:00](https://github.com/Ahmed-Sleem/Pattern-Analysis-Framework-for-cryptocurrency/assets/128150121/a0c7bba3-4d0a-471e-b91e-41275e6a84e9)

![IMAGE 2024-04-21 19:39:16](https://github.com/Ahmed-Sleem/Pattern-Analysis-Framework-for-cryptocurrency/assets/128150121/91cad8b0-99a9-4ca1-8198-48ca39df9f48)

![IMAGE 2024-04-21 19:39:30](https://github.com/Ahmed-Sleem/Pattern-Analysis-Framework-for-cryptocurrency/assets/128150121/f57b515b-a2bc-4746-85b9-6e46742a7c76)

![IMAGE 2024-04-21 19:39:42](https://github.com/Ahmed-Sleem/Pattern-Analysis-Framework-for-cryptocurrency/assets/128150121/f1c4c98b-513d-4e9c-891c-bac93a8701d9)

![IMAGE 2024-04-21 19:39:53](https://github.com/Ahmed-Sleem/Pattern-Analysis-Framework-for-cryptocurrency/assets/128150121/b5a78415-850b-47f2-887f-502783e9c309)

![IMAGE 2024-04-21 19:39:59](https://github.com/Ahmed-Sleem/Pattern-Analysis-Framework-for-cryptocurrency/assets/128150121/7b518574-197a-4d2f-9441-c802c45d82a1)

![IMAGE 2024-04-21 19:40:10](https://github.com/Ahmed-Sleem/Pattern-Analysis-Framework-for-cryptocurrency/assets/128150121/a10a850e-c2b9-4fbe-8521-49187eeb68ad)

![IMAGE 2024-04-21 19:40:17](https://github.com/Ahmed-Sleem/Pattern-Analysis-Framework-for-cryptocurrency/assets/128150121/3d31c0f8-167f-4f5b-afda-a66490c43461)

![IMAGE 2024-04-21 19:40:24](https://github.com/Ahmed-Sleem/Pattern-Analysis-Framework-for-cryptocurrency/assets/128150121/8a6aaed8-06f4-42b5-be4b-5092d1d116f4)




