https://www.youtube.com/watch?v=-frNQkRz_IU
### Quick Reference for Storage Calculations
Here are simplified storage calculations based on the rule of thumb:
- 1M * 1KB = 1GB
- 1M * 10KB = 10GB
- 1M * 100KB = 100GB
- 10M * 1KB = 10GB
- 10M * 10KB = 100GB
- 10M * 100KB = 1TB
- 100M * 1KB = 100GB
- 100M * 10KB = 1TB
- 100M * 100KB = 10TB
  
| Bytes     | Name | Value |
|---------------------------|-----------------------|--------------------------|
|Kilo|Thousand|10^3|
|Mega|Million|10^6|
|Giga|Billion|10^9|
|Tera|Trillion|10^12|
|Peta|Quadrillion|10^15|

Now simply use the above
1Mx10KB = 1x10^6x10x10^3 B = 10GB (add 3+6 =9, 9 corresponds to GB in the above table)


### Data Type Sizes (Approximate)
- Integer: 4 bytes
- String (approx. 10 characters): 20 bytes
- Timestamp: 8 bytes
- UUID: 16 bytes

### RPS Calculation Rule of Thumb
1. Estimate the number of actions per user.
2. Multiply by the number of users (e.g., DAU).
3. Divide by the number of seconds in a day (86,400 seconds) for daily actions.

### Example: Chat System
- **Storage**: Assume each message has:
  - 100 characters (200 bytes for string)
  - User ID (16 bytes)
  - Timestamp (8 bytes)
  - Total per message: 224 bytes ≈ 0.22KB
  - DAU = 10M, each user sends 10 messages/day → 100M messages/day.
  - Storage: 100M * 0.22KB = 22GB/day

- **RPS**: 100M messages/day → \( \frac{100M}{86,400} \approx 1,157 \) RPS.

### 10 Different System Design Problems with Storage and RPS Estimates

| System Design Problem     | DAU (10M) Storage/Day | MAU (100M) Storage/Month | Actions per User/Day | RPS Estimate (Daily) |
|---------------------------|-----------------------|--------------------------|----------------------|----------------------|
| **Chat System**            | 22GB                  | 660GB                    | 10 messages           | 1,157                |
| **Social Feed**            | 100GB                 | 3TB                      | 20 posts viewed       | 2,315                |
| **File Upload System**     | 500GB                 | 15TB                     | 1 upload (5MB)        | 116                  |
| **Ride-Sharing System**    | 10GB                  | 300GB                    | 1 ride                | 116                  |
| **Video Streaming**        | 2TB                   | 60TB                     | 2 videos streamed     | 232                  |
| **E-Commerce Platform**    | 50GB                  | 1.5TB                    | 5 purchases browsed   | 579                  |
| **Notification System**    | 5GB                   | 150GB                    | 5 notifications       | 579                  |
| **Banking System**         | 30GB                  | 900GB                    | 3 transactions        | 347                  |
| **Fitness App**            | 25GB                  | 750GB                    | 10 activity logs      | 1,157                |
| **Email Service**          | 15GB                  | 450GB                    | 5 emails              | 579                  |

### Explanation of Calculation Rules

1. **Storage**: For each design problem, calculate storage per action by summing the size of all relevant fields (e.g., user ID, timestamp, content). Multiply by the number of actions (DAU * actions per user) to get daily storage. Use the rule of thumb (M * K = GB) for fast estimates.
   
2. **RPS**: Estimate the number of actions per user per day, multiply by DAU to get daily actions, and divide by 86,400 seconds to estimate RPS.

This approach allows for quick calculations in interview settings, where precision is less critical than demonstrating reasoning.
