# Flight Display

## Uses cases
1. Big ticker board will fetch upcoming depating flights from the web API
2. Airline admin makes updates to the airline's departure schedule
3. Airline staff makes updates to change the status of a particular flight

## Traffic
### Inbound
1. From big ticker boards
2. From schedule updater app
3. From flight status updater app
4. Customer website where they can check flight statuses (and subscribe to a particular flight)
5. Must handle spikey traffic from point 3

### Outbound
1. Push notifications for customer flight subscriptions

## Data Modelling

### Database constraints
### Indices
#### Uses case 1

## System Design

## Requirements meet, tradeoffs, and assumptions

### Requirements Met
1. Only departing flights are accounted for in the system
2. Airline employees can use the "Update Flight Schedule App" to create/update/delete weekly schedules for flights
3. When a schedule is created or the flight has departed

### Tradeoffs
* Assuming this system will be read-heavy instead of write-heavy. Thus, have gone with a master-slave configuration for the database. This was a conscious decision over using an in-memory cache, such as memcached. The caching strategy (when to warm the cache, when to invalidate etc) seemed more complicated to manage than database replication at this stage.
* We want to abstract the application code from knowing about the master and slaves, and just believe there is only one database at play. This is why we chose to use a db router, such as MySQL Router to route read requests to the slave and write requests to the master. This increases the architectural complexity but reduces the application code complexity. This also makes adding new database instances easier in the future because we just need to make a configuration change in the db router, and not in each of the app servers themselves.
* When the status of the flight changes, we update the column *flight_status* of the corresponding row in the *flights* table. It's important to note that we do not track when the status was changed. If the airline requests for information such as, "tell me how long it took for flight XXX to change from BOARDING to DEPARTED", we would not be able to do so. This might be a viable feature in the future, where the airline might want to view such information in their business intelligence platforms.

### Assumptions
* To update the status of a flight, it is done via a mobile application (e.g. running on an ipad device). An airline personnel managing that particular flight will log into the system, find the flight they are managing, and manually update the flight status at the appropriate times.
* There is no distinction of employee roles, so any employee in the system could update the flight schedules and flight statuses
* The same flight can depart from the same airport once per day. This is a big assumption. The data database table, schedule, is currently modelled in such a way that it does not accomodate same flight departing more than once from the same airport. If this assumption is wrong, we would need to refactor the table layout. Not much data migration is needed because in this system, we almost never care about older flights i.e. once the flight is departed, it is never shown on the big ticker board.
* The system is not required to generate reports. Hence, we do not keep track of audit information such as when a status of a flight was changed.
* A employee can only belong to one airline. That translates to a business constraint, a employee can only work for one airline at a time.

## Development effort
