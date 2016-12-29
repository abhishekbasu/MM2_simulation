# MM2_simulation
The following Ipython notebook (Python 2.7) provides an elegant solution to the M/M/2 simulation and provides interactive charts for visualizing the results.

Definitions: Throughout the discrete-event simulation, we keep track of the current time t, and the corresponding state of the system is given by N(t) which is the total number of customers in the system at time t. The cumulative number of arrivals, denoted by NA(t) is also recorded during the simulation. The time of arrival of the customer (Arrival_Time) and the time to reach the counter (CounterReach_Time) for each customer is recorded. When the customer is allocated to a server, the time taken to service (Service_Time) is recorded. The four events that can occur in this system are: the arrival of a new customer, service completion of a customer at server 1, service completion of a customer at server 2, and the end of simulation at time T. We maintain a separate array for customers who have not been assigned a server yet, and call it “unassigned”. t_a is the time of the next arrival and t_s is the time taken for service of any particular customer. t_d_1 and t_d_2 are the times of next service completion at server 1 and server 2 respectively. T is the time of the end of the simulation. These are the only four events that change the state of the system at each event occurrence, we update the times and keep them sorted.

State of the system: N(t)

Input variables: arrival rate; service rate ; total time T of the simulation.

Here,
 T = 300 minutes

Intermediate variables: The intermediate variables are as follows-
•	t_a
•	t_d_1
•	t_d_2
•	t_s
•	NA(t)
•	And the system state

They have been described under Definition above.

Note: The unassigned arrays hold subset of data presented above for each customer. 
Output from simulation: In the way that I have set up the simulation, following output values could be obtained for each customer:
•	Arrival_Time
•	CounterReach_Time
•	Service_Time

They have been described under Definition above.
Output from calculations: The following outputs were generated for each customer:
•	Queue_Time = total time spent by the user in the queue
•	Departure_Time = time at which user leaves/ departs the system
•	System_Time = total time spent by the user in the system
•	Tmax = Maximum time in the system per customer
•	Arrival_Headway (between customers) = the inter-arrival times

Apart from the above, the following output variables we calculated for the system:
•	N_S (= number of customers in system indexed by minute
•	N_Q () = number of customers in queue indexed by minute
•	N_S_bar () = average number of customers in system
•	N_Q_bar () = average number of customers in queue
•	T_Q_bar () = average time spent in queue per customer
•	T_bar (T) = average time spent in system

Initialization: To begin the simulation, we initialize all the variables and counters: t=0, N(t) = 0, NA(t) = 0. The ‘unassigned’ arrays and the server hash tables are initialized to be empty.

We then start the simulation by generating the first events of each type. To generate the arrival of the first customer, we draw a random sample (random_arrival). Since the system is empty in the beginning with no customer being served, we set t_d_1 and t_d_2 both to be equal to big_num. The end of the simulation is constant at T (= 300 minutes).

Note:
1.	big_num = same as (1/epsilon). Theoretically .
2.	random_arrival = a randomly generated arrival time. Theoretically = . Here u is a uniform random sample in the interval (0,1). 
3.	random_service = a randomly generated service time. Theoretically = . Here u is a uniform random sample in the interval (0,1). 
4.	We keep track of customers in queue by storing their arrival times in arrays called ‘unassigned’.
5.	We keep track of the customers being served at each individual server, with separate hash tables. Assigning/ allocating a customer to a server simply implies that we append the values (Arrival_Time, CounterReach_Time, Service_Time) of the customer at that specific server’s hash table. The entries are thereby transferred and space is freed in the ‘unassigned’ array.
6.	The notation t_d_i represents the service completion time at server i, where i is 1 or 2 depending on where it is mentioned in the subsequent text.
7.	The scripts 1 and 2 refer to the 2 servers. Under the present scenario, there’s no way to distinguish between the servers, hence either may be recognized as Arthur and hence the other would naturally refer to Bert.

The distinct events are as follows:

Event 1: This event occurs if t_a = min{t_a, t_d_1, t_d_2, T}. This the event of an arrival.
We assign the incoming customer to the “unassigned” array and record their time of arrival (Arrival_Time).
•	The simulator’s time is advanced: t = t_a.
•	The system’s state is updated as follows: N(t) = N(t) + 1
•	The cumulative arrival counter is updated: NA(t) = NA(t) + 1
•	The next arrival time is generated: t_a = t + random_arrival()
•	Execute triggered events:
o	If N(t) == 1: newly arrived customer begins service immediately. A random service time is generated t_s = random_service(). With equal probability (generated using a uniform random variable v and checked if v is above or below 0.5) he is allocated to one of the servers. We record the time t as being the time at which he reaches the counter (CounterReach_Time) and t_s as the time taken for service (Service_Time). Depending on the server chosen, t_d_i is updated as t_d_i = t +t_s.
o	If N(t) == 2: newly arrived customer begins service immediately. A random service time is generated t_s = random_service(). We check which of the servers is free (by comparing t_d_i to big_num)* and allocate the customer to that server. We record the time t as being the time at which he reaches the counter (CounterReach_Time) and t_s as the time taken for service (Service_Time). Depending on the server who is free and performs service for this particular customer, t_d_i is updated as t_d_i = t +t_s.
o	If  N(t) > 2: newly arrived customer waits in queue.

Event 2: This event occurs if t_d_1 = min{t_a, t_d_1, t_d_2, T}. This is the event of a service completion at server 1.
•	The simulator’s time is advanced: t = t_d_1
•	The state is updated as follows: N(t) = N(t) - 1
•	The next service completion time is generated as follows.
o	If N(t) == 0: The system is empty. t_d_1 is set to big_num.
o	If N(t) == 1: There is only one customer in system and is being served at the other counter. t_d_1 is set to big_num.
o	If N(t) >= 2: A customer waiting in the queue is served. A random service time is generated t_s = random_service(). We take one customer from the unassigned array (the customer who arrived the earliest to join the queue and is yet to be served).  We record the time t as being the time at which he reaches the counter (CounterReach_Time) and t_s as the time taken for service (Service_Time). We update t_d_1 = t + t_s.

Event 3: This event occurs if t_d_2 = min{t_a, t_d_1, t_d_2, T}. This is the event of a service completion at server 2.
•	The simulator’s time is advanced: t = t_d_2
•	The state is updated as follows: N(t) = N(t) – 1
•	The next service completion time is generated as follows.
o	If N(t) == 0: The system is empty. t_d_2 is set to big_num.
o	If N(t) == 1: There is only one customer in system and is being served at the other counter. t_d_2 is set to big_num.
o	If N(t) >= 2: A customer waiting in the queue is served. A random service time is generated t_s = random_service(). We take one customer from the unassigned list (the customer who arrived the earliest to join the queue and is yet to be served).  We record the time t as being the time at which he reaches the counter (CounterReach_Time) and t_s as the time taken for service (Service_Time). We update t_d_2 = t + t_s.

Event 4: This event occurs if T = min{t_a, t_d_1, t_d_2, T}.
•	At this event the discrete-event simulation stops and t = T.
* When t_d_i is equal to big_num, it implies that the server is free.

Additional notes on notation:
!= : this symbol implies “is not equal to”
== : this symbol implies “is equal to”
wherever used.

The simulation for the customer service process was run for T= 300 minutes
 
From the sample output, we infer that:

The average time spent in the system per customer () = 6.76022 minutes
Maximum time in the system per customer () = 21.5673 minutes
Average time spent in the queue per customer () = 4.28238 minutes
Average number of customers in the system () = 4.83666 customers
Average number of customers in the queue () = 3.06 customers
