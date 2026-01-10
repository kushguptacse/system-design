# system-design
Scalability -
1.Horizontal and vertical scalability.
1.1 Increasing capacity of single machine is vertical scaling. Increasing number of machine is horizontal scaling. 
1.2 server can be taken on premise or from cloud provider like aws, gcp, azure. And if we want to execute some code without need of server serverless option can be used like lambda. 
2. Database scaling : how database can be scalled and handle fail over. 
2.1 cold standby: from primary db periodic back up is taken into disk. So in case of failure of db server, a new stand by server can be setup to restore from back up. In this approach downtime will be more till stand by is getting data restored from last successful back up.
2.2 warm standby: in this approach secondary db will keep on syncing with primary using replication. So in case primary goes down secondary already ready to serve. Just a switch seconds needed.
2.3 hot standby: in this approach stand up by database is synced real time and is no longer just stand by, it will be used to handle read request. So if primary goes down it can handle write also and parallelly separate db can be made ready to join it as standby.
2.4 multi-primary: in this approach primary and secondary both can read and write simultaneously and hence traffic also reduced. It is kind of similar to horizontal scaling of server. 
