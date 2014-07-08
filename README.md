CommonSourceIPs
===============

# GOAL
# 	Determines the top 10 most common source IP addresses, and their hit rates, 
# 	for a fleet of 1000 web servers within the last hour.
#
# The following assumptions may be used...
# 	* web servers are locally writing access logs in the Apache Combined Log 
# 	Format (http://httpd.apache.org/docs/current/logs.html#combined).
# 	* web servers are accessible by ssh.

# SOLUTION
#	1. For each server in the fleet, copy the /var/log/httpd/access_log to
#	local directory by ssh.
#	2. Given a access_log, for each line in the log, extract the IP address, 
#	time/time zone and status code. 
#	3. For lines written in the past hour, count the occurrences of each IP 
#	address(number of requests) and numbers of sucessful operations(status code
#	1xx,2xx and 3xx in my implementation).
#	4. After counting logs of all servers, sort the IP addresses by their occurrences.
#	5. For the top 10 IP addresses, calculate their hit rates using following
#	equation:
#		hit rate = (# of successful operations) / (# of all requests)

# ASSUMPTIONS
# 	1. The names of servers in the fleet are stored in a file, such as 'fleet_servers'. 
# 	The servers can be geographically distributed in different cities/countries.
# 	2. This script will be executed in a directory that can be read & written by the user. 
# 	3. The report of the top 10 most common IPs and hit rates will be stored in current 
# 	directory.
# 	4. The SSH keys to remote servers have been generated and synced, so that there 
# 	is no need to manually enter the passwords. 
# 	3. Following Perl modules should be installed:
# 		DateTime - used to determine if the logs are older than 1 hour.
# 		Parallel::ForkManager - check multiple logs in concurrently.

# USAGE
#  analyse_http_log.pl [-i server_list] [-o report] [-l login] [-a access_log]
#   		[-t time_range] [-n ip_num] [-p process_num]
#  where:
#  	server_list	 - list of servers in the pool, optional
#  	report	 - report file, optional 
#  	login		 - login to admin servers, optional
#  	access_log	 - path of Apache access_log, optional
#  	time_range	 - range of time to be analysed(unit: hour), optional
#  	ip_num	 - number of the top source IPs, optional
#  	process_num	 - number of concurrent processes, optional

# 	Bo Yang, bonny95@gmail.com, 07/03/2014
 
