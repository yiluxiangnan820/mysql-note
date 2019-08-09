1、Primary key
It is recommended that the primary key set ‘AUTO_INCREMENT’ to prevent page splitting frequently.

2、Unique index is not recommended
Unique index can not use change buffer mechanism. 
If it is acceptable in application,usually it brings about good performance with non-unique index  .

Change buffer concept
if a index page needs to be updated and the index page in memory ,then the index page will be updated directly.
if a index page needs to be updated and the index page not in memory,InnoDB will cache these update operations in the 
change buffer without affecting data consistency, so that the index page does not need to be read into memory immediately. 
When you need to access the data page, read the data page into memory, and then write the update information about the page 
in the change buffer. This process is called merge.
In addition to merge execution when accessing pages, background threads also periodically merge, introducing 
change buffers to reduce disk reads and improve buffer pool space utilization

Reason:
From the point of view of query process, non-unique index needs to query the next record and judge 
whether it meets the query condition more than once every query. 
The effect of this operation on query performance can be neglected. 
If the current record is the last record on the current page, query next data requires reading the next data page.
This process is slightly more complicated, but the probability is small.

From the point of view of the update process, if the updated index page is in memory, the non-unique index performs updates directly, 
the unique index judges whether there is conflict or not, and there is no conflict updates; 
if the pages that need to be updated are not in memory, the non-unique index records updates in the change buffer, 
and the unique index needs to read page data into memory, checking for error conflicts. Update page data directly without conflict.