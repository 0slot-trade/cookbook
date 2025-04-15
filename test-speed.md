# How to Test Speeds of Different Server Nodes

This article explains how to use a Python script to test the response speeds of different server nodes. Based on the test results, you can select the optimal node. We'll demonstrate the entire testing process with a practical code example.

## 1. Preparations

Before starting the test, ensure you have:
- A Python environment (Python 3.6+ recommended).
- The `requests` and `numpy` libraries for sending HTTP requests and calculating percentiles.
- A list of server node URLs and corresponding API keys (if required).

### Install Dependencies

First, install the `requests` and `numpy` libraries if not already installed:

```bash
pip install requests numpy
```

## 2. Writing the Test Script

We'll create a Python script to test server node response speeds. The script will send multiple HTTP requests, collect response times, and calculate average latency, min/max latency, and percentile latencies.

Here's the complete script:

```python
import requests
import time
import numpy as np


def test_url(url, num_requests):
    """
    Tests the performance of a URL by making multiple HTTP requests.
    
    Args:
        url (str): The URL to be tested
        num_requests (int): Number of requests to make
        
    Returns:
        tuple: (average_time, min_time, max_time, percentiles) if successful
               (None, None, None, None) if no successful requests
    """
    # Create a session object for connection pooling
    session = requests.Session()
    
    # Initialize performance metrics
    total_time = 0
    successful_requests = 0
    min_time = float('inf')  # Initialize with infinity
    max_time = 0 
    times = []  # List to store all response times
    
    # Make multiple requests to test performance
    for _ in range(num_requests):
        try:
            # Record start time before making request
            start_time = time.time()
            
            # Make GET request to the URL
            response = session.get(url)
            
            # Record end time after receiving response
            end_time = time.time()
            
            # Calculate elapsed time for this request
            elapsed_time = end_time - start_time
            
            # Update metrics
            total_time += elapsed_time
            successful_requests += 1
            times.append(elapsed_time)

            # Update min and max times
            if elapsed_time < min_time:
                min_time = elapsed_time
            if elapsed_time > max_time:
                max_time = elapsed_time
                
        except requests.exceptions.RequestException as e:
            print(f"Request failed: {e}")

    # Calculate and display statistics if we had successful requests
    if successful_requests > 0:
        # Calculate average response time
        avg_time = total_time / successful_requests
        
        # Print basic statistics
        print(f"Successful requests: {successful_requests}/{num_requests}")
        print(f"Average time: {avg_time:.6f} seconds")
        print(f"Minimum time: {min_time:.6f} seconds")
        print(f"Maximum time: {max_time:.6f} seconds")

        # Calculate various percentiles (10% to 90%)
        percentiles = np.percentile(times, [10, 20, 30, 40, 50, 60, 70, 80, 90])
        
        # Print percentile information
        for i, percentile in enumerate(percentiles):
            print(f"{10 * (i + 1)}% percentile: {percentile:.6f} seconds")

        return avg_time, min_time, max_time, percentiles
    else:
        print("No successful requests.")
        return None, None, None, None


# Number of requests to be made for testing
num_requests = 1000

# URLs of the server nodes to be tested
de_domain_url = "http://de1.0slot.trade/?api-key=xxx"
ny_domain_url = "http://ny1.0slot.trade/?api-key=xxx"
ams_domain_url = "http://ams1.0slot.trade/?api-key=xxx"


print("Testing de_domain_speed...")
test_url(de_domain_url, num_requests)

print("Testing ny_domain_speed...")
test_url(ny_domain_url, num_requests)

print("Testing ams_domain_speed...")
test_url(ams_domain_url, num_requests)
```

### Code Explanation

1. **Function Definition**:
   - `test_url(url, num_requests)`: Sends multiple HTTP requests, collects metrics, and calculates statistics.
   - Parameters:
     - `url`: Server node URL to test.
     - `num_requests`: Number of requests to send.
   - Returns:
     - Tuple with metrics if successful; `None` otherwise.

2. **Test URLs**:
   - `de_domain_url`: Frankfurt node URL.
   - `ny_domain_url`: New York node URL.
   - `ams_domain_url`: Amsterdam node URL.
   - Replace `api-key=xxx` with valid API keys.

3. **Test Execution**:
   - Sends 1000 requests to each node.
   - Prints performance statistics for each node.

## 3. Running the Test

Save the script as `test_speed.py` and run:

```bash
python test_speed.py
```

Sample output:

```
Testing de_domain_speed...
Successful requests: 1000/1000
Average time: 0.281030 seconds
Minimum time: 0.211010 seconds
Maximum time: 0.784061 seconds
10% percentile: 0.271020 seconds
20% percentile: 0.271345 seconds
...
```

## 4. Analyzing Results

The output shows average, min/max, and percentile latencies. Use this data to select the fastest node.

## Troubleshooting Server Response Issues

If server response times seem abnormal:

### 1. Use the `ping` Tool to Test Response Time
`ping` measures round-trip time (RTT) between your machine and the server.

- **Steps**:
  ```bash
  ping <server_ip_or_domain>
  ```
- **Analysis**:
  - High RTT may indicate network/server issues.

### 2. Use `traceroute` to Analyze Network Paths
Identifies network hops and their latency:

- **Windows**:
  ```bash
  tracert <server_ip_or_domain>
  ```
- **MacOS/Linux**:
  ```bash
  traceroute <server_ip_or_domain>
  ```
- **Analysis**:
  - Look for high-latency hops or suboptimal routing.

### 3. Contact ISP or Consider Server Migration
- Contact your ISP if routing issues are found.
- Consider migrating servers closer to your user base.

---

Need help? Contact us via:  
- **Discord Support**: [Join our community](https://discord.com/invite/Qd6txfyS)  
- **Twitter/X**: [Follow @0slot_trade](https://x.com/0slot_trade)  
- **Telegram**: [Message @kurt0slot](https://t.me/kurt0slot)
