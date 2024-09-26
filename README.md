# Design-Analysis-of-Algorithm---Second Project
# The project is to implement and analyze the deterministic Quick Select algorithm, which employs the Median of Medians method. 
import matplotlib.pyplot as plt
import pandas as pd
import time
operation_count = 0
def insertion_sort(arr):
   global operation_count
   for i in range(1, len(arr)):
       key = arr[i]
       j = i - 1
       while j >= 0 and key < arr[j]:
           arr[j + 1] = arr[j]
           j -= 1
           operation_count += 1
       arr[j + 1] = key
   return arr
def median_of_medians(arr, i):
   global operation_count
   sublists = [arr[j:j+5] for j in range(0, len(arr), 5)]
   medians = [insertion_sort(sublist)[len(sublist) // 2] for sublist in sublists]
   if len(medians) <= 5:
       i = min(i, len(medians) - 1)
       return insertion_sort(medians)[i]
   else:
       new_i = min(i, len(medians) // 2)
       return median_of_medians(medians, new_i)
def partition(arr, pivot):
   global operation_count
   low = [x for x in arr if x < pivot]
   high = [x for x in arr if x > pivot]
   operation_count += len(arr)
   return low, [pivot], high
def quickselect(arr, k):
   global operation_count
   if len(arr) < 10:
       return insertion_sort(arr)[k]
   else:
       pivot = median_of_medians(arr, len(arr) // 2)
       low, mid, high = partition(arr, pivot)
       L = len(low)
       if k < L:
           return quickselect(low, k)
       elif k < L + len(mid):
           return mid[0]
       else:
           return quickselect(high, k - L - len(mid))
# New test data with increasing input sizes
new_input_data = [
   ([i for i in range(1, 11)], 4),     # Array of size 10
   ([i for i in range(1, 21)], 10),    # Array of size 20
   ([i for i in range(1, 31)], 15),    # Array of size 30
   ([i for i in range(1, 41)], 20),    # Array of size 40
   ([i for i in range(1, 51)], 25),    # Array of size 50
   ([i for i in range(1, 61)], 30),    # Array of size 60
   ([i for i in range(1, 71)], 35),    # Array of size 70
   ([i for i in range(1, 81)], 40),    # Array of size 80
   ([i for i in range(1, 91)], 45),    # Array of size 90
   ([i for i in range(1, 101)], 50)    # Array of size 100
]
# Collect results for table and graph with the updated requirements
new_table_data = []
for arr, k in new_input_data:
   operation_count = 0
   n = len(arr)
   # Measure real execution time
   start_time = time.time()
   quickselect(arr.copy(), k)
   end_time = time.time()
   real_time = end_time - start_time  # Actual time in seconds
   theoretical_time = n  # Linear time complexity for theoretical time
   scaled_theoretical_time = theoretical_time / 10  # Arbitrary scaling for visualization
   new_table_data.append({
       "N": n,
       "Experimental Time (seconds)": real_time,
       "Theoretical Time (N)": theoretical_time,
       "Scaled Theoretical Time": scaled_theoretical_time,
       "Time Complexity": "O(N)"  # Adding the time complexity column
   })
# Creating a DataFrame for better visualization
df_final_real_time = pd.DataFrame(new_table_data)
# Display the updated table with real execution times
print(df_final_real_time)
# Extracting data for graph plotting
input_sizes = df_final_real_time['N']
experimental_times = df_final_real_time['Experimental Time (seconds)']
theoretical_times = df_final_real_time['Theoretical Time (N)']
# Plotting the graph
plt.figure(figsize=(10, 6))
plt.plot(input_sizes, experimental_times, marker='o', linestyle='-', color='b', label="Experimental Time (seconds)")
plt.plot(input_sizes, theoretical_times, marker='x', linestyle='--', color='r', label="Theoretical Time (N)")
plt.title('Input Size vs Time: Theoretical vs Experimental')
plt.xlabel('Input Size (N)')
plt.ylabel('Time (seconds)')
plt.legend()
plt.grid(True)
# Show the graph
plt.show()
