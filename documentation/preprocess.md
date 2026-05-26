-- Pre Process Guide (Using Python jupyternotebook)

-- Use function and validate (comment function with """"""")

-- reusabaility, scalabaility

-- comment on codes

-- Do not concat first, load each csv file (for example, for claimant count, load claimaount count 2017, clean and preprocess it to be concat later)

-- standardise column names

-- for claimant:
    - keep LSOA Code, lSOA Name, pop, claimant rate
    - remove duplicates using lsoa code (find average claimant rate and claimant count)
    - claimant rate in 2 dp


-- for crime:
    - keep LSOA Code, total crime rate (addition of all the columns with "rates" for that row)
    - remove duplicates using lsoa code (find average crime rate)
    - total crime rate in 2 dp

-- for health
    -keep LSOA Code, total_prevelance_rate (addition of all the columns with "rates" for that row)
    - remove duplicates using lsoa code (find average total prevelance rate)
    - total_prvelance_rate in 2 dp

-- do this for all the years (while adding year column from folder name)

-- calculate ADI by adding claimant rate, total crime rate, total prevelance rate using lsoa code

-- final output, lsoa code, lsoa name, pop, ADI as csv file
