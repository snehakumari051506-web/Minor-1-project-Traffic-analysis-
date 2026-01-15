import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from datetime import datetime, timedelta

# 1. Generate Synthetic Traffic Data
np.random.seed(42)

dates = pd.date_range(start="2025-01-01", periods=168, freq="H")  # 1 week of hourly data
junctions = ['Junction_A', 'Junction_B']

data = []
for junction in junctions:
    for date in dates:
        # Simulate peak hours (morning 8-10, evening 17-19)
        base_traffic = np.random.randint(10, 50)
        hour = date.hour
        if 7 <= hour <= 9 or 16 <= hour <= 18:
            base_traffic += np.random.randint(40, 100)
        data.append([date, junction, base_traffic])

df = pd.DataFrame(data, columns=['DateTime', 'Junction', 'VehicleCount'])

# 2. Preprocessing

df['Hour'] = df['DateTime'].dt.hour
df['Day'] = df['DateTime'].dt.day_name()

# 3. Visualization - Line Plot (Trends over time)
plt.figure(figsize=(12, 5))
sns.lineplot(data=df, x='DateTime', y='VehicleCount', hue='Junction')
plt.title('Traffic Volume Trends Over One Week')
plt.xticks(rotation=45)
plt.show()

# 4. Visualization - Heatmap (Hourly Density)

# Pivot data for heatmap (Average vehicles per hour per day)
pivot_df = df.pivot_table(values='VehicleCount', index='Day', columns='Hour', aggfunc='mean')
# Reorder days for logical flow
days_order = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday']
pivot_df = pivot_df.reindex(days_order)



plt.figure(figsize=(14, 6))
sns.heatmap(pivot_df, annot=True, fmt=".0f", cmap="YlGnBu", cbar_kws={'label': 'Avg Vehicles'})
plt.title('Hourly Traffic Density Heatmap (Average Vehicles)')
plt.xlabel('Hour of Day')
plt.ylabel('Day of Week')
plt.show()

