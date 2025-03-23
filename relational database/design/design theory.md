### Data Redundancy
Think about this: there is a table that
- Hold information about name, SSN, phone, and city
- Associate people with the city they live in
- Associate people with any phone numbers they have

| Name | SSN          | Phone        | City           |  
|------|-------------|-------------|---------------|  
| Fred | 123-45-6789 | 206-555-9999 | Seattle       |  
| Fred | 123-45-6789 | 206-555-8888 | Seattle       |  
| Joe  | 987-65-4321 | 415-555-7777 | San Francisco |  

Anomalis:
- We see the redudancy of the table. This imply that the update would be slow.
- We see that the only difference in Freds' entry is the phone. How would one delete just one of the phone

To avoid it, we can break down the table

| Name | SSN          | City           |  
|------|-------------|---------------|  
| Fred | 123-45-6789 | Seattle       |  
| Joe  | 987-65-4321 | San Francisco |  

| SSN          | Phone        |  
|-------------|-------------|  
| 123-45-6789 | 206-555-9999 |  
| 123-45-6789 | 206-555-8888 |  
| 987-65-4321 | 415-555-7777 |  

### Functional Dependencies (FDs)
