import pandas as pd
import numpy as np

# --------------------------
# Step 1: Sample Data Creation
# --------------------------

data = {
    'Device': ['Refrigerator', 'AC Unit', 'Heater', 'Washing Machine', 'TV',
               'AC Unit', 'Heater', 'TV', 'Refrigerator', 'Washing Machine'],
    'Timestamp': [
        '2025-05-06 10:00', '2025-05-06 14:00', '2025-05-06 06:00', '2025-05-06 18:00', '2025-05-06 20:00',
        '2025-05-07 13:00', '2025-05-07 05:00', '2025-05-07 21:00', '2025-05-07 10:00', '2025-05-07 17:30'
    ],
    'Usage_kWh': [0.8, 3.0, 2.5, 1.2, 0.5, 2.9, 2.3, 0.7, 0.9, 1.1]
}

df = pd.DataFrame(data)
df['Timestamp'] = pd.to_datetime(df['Timestamp'])
df['Date'] = df['Timestamp'].dt.date
df['Hour'] = df['Timestamp'].dt.hour

# --------------------------
# Step 2: Summary Statistics
# --------------------------

# Total usage per device
total_usage = df.groupby('Device')['Usage_kWh'].sum().sort_values(ascending=False)

# Daily usage per device
daily_usage = df.groupby(['Device', 'Date'])['Usage_kWh'].sum().reset_index()

# Peak hour usage
peak_hours = range(17, 21)  # 5 PM to 8 PM
df['PeakHour'] = df['Hour'].apply(lambda h: h in peak_hours)
peak_usage = df[df['PeakHour']].groupby('Device')['Usage_kWh'].sum()

# --------------------------
# Step 3: Efficiency Scoring
# --------------------------

# Define scoring based on usage patterns
def compute_efficiency(device):
    total = total_usage.get(device, 0)
    peak = peak_usage.get(device, 0)
    if total == 0:
        return 100  # no usage = very efficient
    peak_ratio = peak / total
    score = max(0, 100 - (peak_ratio * 100))  # less peak = more efficient
    return round(score, 2)

efficiency_scores = {device: compute_efficiency(device) for device in total_usage.index}

# --------------------------
# Step 4: Generate Recommendations
# --------------------------

recommendations = []

for device in total_usage.index:
    total = total_usage[device]
    peak = peak_usage.get(device, 0)
    score = efficiency_scores[device]

    if total > 4.0:
        recommendations.append(f"{device}: High total usage ({total} kWh). Consider upgrading or reducing usage.")
    if peak > 2.0:
        recommendations.append(f"{device}: Frequent usage during peak hours ({peak} kWh). Try shifting to off-peak.")
    if score < 60:
        recommendations.append(f"{device}: Low efficiency score ({score}). Optimization needed.")

# --------------------------
# Step 5: Output Results
# --------------------------

print("=== Total Usage per Device ===")
print(total_usage, "\n")

print("=== Daily Usage ===")
print(daily_usage, "\n")

print("=== Peak Hour Usage ===")
print(peak_usage, "\n")

print("=== Efficiency Scores ===")
for device, score in efficiency_scores.items():
    print(f"{device}: {score}/100")

print("\n=== Recommendations ===")
for rec in recommendations:
    print("- " + rec)
