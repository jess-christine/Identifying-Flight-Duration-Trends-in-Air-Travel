# Identifying Flight Duration Trends in Air Travel

A data analysis project analyzing air travel patterns, flight durations, and destination metrics for flights departing from major New York City airports during the second half of 2022. Built using R and data manipulation packages from the Tidyverse (`dplyr`, `readr`), this analysis provides operational insights into flight frequency and duration trends.

---

## 📌 Project Overview

Optimizing flight schedules and understanding travel patterns require granular analysis of operational aviation data. Based on the `nycflights2022` dataset collection by the ModernDive team, this project evaluates flight operations originating from NYC’s primary airports:
* **JFK**: John F. Kennedy International Airport
* **LGA**: LaGuardia Airport
* **EWR**: Newark Liberty International Airport

Key objectives include identifying the most frequent flight routes, determining longest average flight durations across carriers and destinations, and uncovering rare destination routes departing from JFK.

---

## 🛠️ Data Architecture & Tech Stack

* **Language**: R
* **Libraries**: `dplyr`, `readr`
* **Environment**: Jupyter Notebook / R Kernel
* **Data Sources**:
  * `flights2022-h2.csv` (218,802 flight records covering carriers, origins, destinations, and air time)
  * `airlines.csv` (16 records mapping carrier codes to full airline names)
  * `airports.csv` (1,251 records mapping FAA codes to airport names and locations)

### Data Schema Overview

| File | Field | Description |
| :--- | :--- | :--- |
| `flights2022-h2.csv` | `carrier`, `origin`, `dest`, `air_time` | Airline code, origin/destination codes, duration in air (minutes) |
| `airlines.csv` | `carrier`, `name` | 2-letter airline code and official airline name |
| `airports.csv` | `faa`, `name` | 3-letter FAA airport code and full airport name |

---

## 🔍 Data Transformation & Analysis Pipeline

The analysis joins relational flight data, converts flight durations from minutes to hours, and computes aggregate frequency and duration metrics.

```r
# Import required packages
library(dplyr)
library(readr)

# Load raw datasets
flights  <- read_csv("flights2022-h2.csv")
airlines <- read_csv("airlines.csv")
airports <- read_csv("airports.csv")

# Join relational datasets and standardize column labels
complex_join <- flights %>%
  left_join(airlines, by = "carrier") %>%
  rename(airline_name = name) %>% 
  left_join(airports, by = c("dest" = "faa")) %>% 
  rename(airport_name = name)

# Transform air time from minutes to hours
transformed_data <- complex_join %>%
  mutate(flight_duration = air_time / 60)

# Aggregate average flight duration and flight count per airline-airport route
analysis_result <- transformed_data %>%
  group_by(airline_name, airport_name) %>%
  summarize(
    avg_flight_duration = mean(flight_duration, na.rm = TRUE),
    count = n()
  ) %>%
  ungroup()

# Key Insights Calculations
frequent <- analysis_result %>% arrange(desc(count)) %>% head(1)
longest  <- analysis_result %>% arrange(desc(avg_flight_duration)) %>% head(1)

# Identify least common destination departing specifically from JFK
least_jfk <- transformed_data %>% 
  filter(origin == "JFK") %>% 
  group_by(airport_name) %>% 
  summarize(count = n()) %>% 
  arrange(count) %>% 
  head(1)
```
## 📊 Summary of Insights

* **Most Frequent Route**: Identified top airline-destination pair operating out of the NYC area based on total flight volume ($n$).
* **Longest Flight Duration**: Pinpointed the carrier and destination combination recording the maximum average air time in hours.
* **Least Frequent JFK Destination**: **Eagle County Regional Airport** recorded the fewest departures ($n = 17$) from John F. Kennedy International Airport (JFK) during H2 2022.

---

## 🚀 How to Run

1. **Clone the Repository**:
   ```bash
   git clone [https://github.com/jess-christine/Identifying-Flight-Duration-Trends-in-Air-Travel.git](https://github.com/jess-christine/Identifying-Flight-Duration-Trends-in-Air-Travel.git)
   cd Identifying-Flight-Duration-Trends-in-Air-Travel
   
2. **Setup R Environment:**
Ensure R is installed along with the required tidyverse libraries:
```r
install.packages(c("dplyr", "readr"))
```
3. **Execute Notebook:**
Open and execute 'notebook.ipynb' using Jupyter Notebook, JupyterLab, or VS Code with the R kernel enabled.
