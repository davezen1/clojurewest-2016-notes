Clojure West 2016
===================

Notes from the Clojure West Conference

----------


Specter: Powerful and Simple Data Structure Manipulation by Nathan Marz
-------------

 - https://github.com/nathanmarz/specter
 - http://nathanmarz.com/blog/functional-navigational-programming-in-clojurescript-with-sp.html
 - defn map-vals common pattern (source: Github)
 - About reconstruction - how to avoid complexity with data structures
 - Specter 

> 

    ;; Map-vals
    (print-results
	    (transform [ALL LAST]
	    inc
	    {:a 1 : b 2 : c 3})
    )

 - Navigation independent abstraction, disassociated from the desired transformation
 - handful of navigators 
 - 100 functions on 1 data struct...

> Blockquote

    (print-results
	    (setval [:a (subset #{})]
	    #{1}
	    DATA)
    )
    

 - Querying with Navigation paths
 - Example of bank transfer 
 - Performant 
	 - use compiled paths
 - Examples with DAGs Directed Acyclical Graphs

Managing One of the World's Largest Clojure Code Bases by Donevan Dolby
-------------

 - Base 2 Consultancy
 - Boeing 737 Max
 - 16K lines of Clojure
 - Messages sent to technician in realtime 6K+
 - OMF during assembly Manufacting
 - Maintenance
	 - Passenger Service
	 - Cargo Baggage
	 - Airplane Servicing 
 - Before Huge Hardware doesnt fit on 737
 - Onboard Network System
	 - REST api
		 - Flight Deck
		 - Maintenance Device
	 - Sensors
	 - Ground System
 - 1700+ Requirements
 - Why Clojure
	 - JVM
	 - Functional Reactive
	 - Parallel Processing
	 - Instaparse - DSL vs ANTLR
		 - https://github.com/Engelberg/instaparse
 - Challenges
	 - Managing Dependencies
		 - Puppet TrapperKeeper
			 - https://github.com/puppetlabs/trapperkeeper
		 - Circular Deps Solution
			 - Sub/Pub Messaging Service
			 - Fault Engine and Flight Phase
		 - Messaging Services
			 - Pub/Sub
				 - Lamina Channels (deprecated)
					 - https://github.com/ztellman/lamina
					 - New https://github.com/ztellman/aleph
					 - 
				 - Filter 
		 - Lessons Learned
			 - Performance
				 - YourKit profiling TOols
				 - Mem leaks
				 - https://www.yourkit.com/
				 - Cognitect help
				 - Transducers 1.6 to 1.7
				 - Use Java Directly
					 - ConcurrentLinkedQueue instead of clojure Persistent Queue
				 - Library Selection
					 - Lamina got deprecaated
					 - HUG SQL could have helped
					 - Reports {{mustache}} 
						 - CloseStache at first
						 - Stencil instead
					 - Wrap Libraries - use abstraction around it so you can sub it out
				 - New Developers
					 - OO => Functional 
					 - Immutable State concepts 
					 - Week or two after pairing with vets
					 - pair programming all were newbies
				 - Dev Process
					 - Code Reviews quicker, less code
					 - Pull requests smaller
					 - Unit Testing encouraged with Clojure
					 - Code Coverage with Clover
				 - Still use Clojure???
					 - Could have done it with Node 
					 - Still OO in the design so Java could have made more sense
 - Q & A
		 - Boeing lists Clojure as an approved language
		 - Overall maintenance not keeping plane in the air
		 - 6 Clojure Devs QA Managers = 15 
		 - LightTable, IntelliJ

## Creating DSLs - A tale of spec-tacular success and failure by Claire Alvis
-------

- SparkFund
- DSL
- Datomic
	- Audit append only
	- functional
	- **data driven**
		- vector of keywords
		- :db/add
		- :db/retract
		- pulls are data
	- Data immutable
	- Spec-tactular DSL
		- Datomic more safe and convenient
		- ENtities should have types
		- Specs instead of Schemas
	- Not perfect 0.6.0
	- Design Tradeoffs
- github.com/SparkFund/spec-tactular
- sparkfund.guthub.io/spec-tacular
- @chckadee
- sparkfund.co


## Loom
-------



## Building a Legal Data Service with Clojure
Jonathan Boston, Caleb Phillips
-------

 - Ediscovery  at cicayda
	 - collect relevant docs
	 - lawyers filter documents
	 - produce documents and give to other side
	 - Then
	 - Comp B import produced docs
	 - lawyers search docs
	 - find hot documents
 - Tech Stack
	 - Clojure
	 - ClojureScript
	 - Datomic
	 - AWS
 - BackEnd with Caleb - Desigining with Constraints
	 - what went wrong 
	 - how they are getting better
	 - Consider Constraints the system will face to find useful abstractions
	 - 'Design is making Decisions' Hickey, Rich
	 - Environment
		 - 100s millions of facts regularly -> multitenant system (cases)
		 - variation in cardinality
		 - EC2 + threads
		 - Datomic
			 - well documented tradeoffs 
			 - canary in coal mine 
			 - write thru put tradeoff for ACID txs
			 - didnt asks what happens when he hit limitation
			 - hope as a strategy
			 - result: 
				 - no coordination of writes
				 - answer to limits? transactor unavailable exceptions
				 - variety of bulk tx jobs - threads batching
			 - change/pivot to fix -> Getting Better Discovered Constraints to Drive Decisions
			 - Missing Decisions
				 - batch size, thread count
				 - handle errors/back off /retry
				 - annotate txs with job info for history
				 - track progress 
				 - pause resume
				 - coordinate back pressure with other jobs
				 - Datomic - single pipe so we need single pipe/queue
					 - priorities
					 - share capacity btw jobs/customer
			 - Decisions maade
				 - Single Datomic bulk component
				 - Communicate via queues (AWS)
				 - multiplexer for resource sharing 
				 - extract job tracking to separate data store
				 - fact producers need to define identity
			 - Design Payoffs
				 - more data oriented now
				 - take apart needs apparent -> what needed compoennts
				 - ad hoc jobs w/o fear
				 - scaling is component vs job
				 - Get bertter earlier
				 - Find constraints -? Release It! colleague wrote book
 - Designing UI 
	 - Clojurescript Experience
		 - this, equality, 'namespaces in JavaScript
		 - JS interop is a breeze
		 - Figwheeel
		 - BaconsJs
		 - Prismatic Schema
		 **- Om
		 - Component
		 - DataScript
		 - core.async**
		 - Upgrades
			 - choice of using ClojureScript
			 - FRP jquery/BaconJS -> React/Om
			 - Map -> DataScript (Datomic/Frontent)
			 - Callbacks -> core.async (channels)
			 - Result:
				 - Missing Decisions
					 - over fetching
					 - partial/stale app state
					 - undo /redo
					 - save decisions vs actions
					 - communitcating novelty/identity
				 - Library Driven Development
					 - rewrite every 6 weeks
					 - @thepracticaldev
				 - Design Driven Development
					 - write to clarify thinking with yourself and others
				 - Decisions Made
					 - UI is a system
					 - in process
					 - libraries don't define the system
					 - less churn if you think about your design
				 - Is it working?
				 - Design Comp and performance Rich Hickey
				 - Simple Made easy
				 - Components just Enuff Strucutre stuare siera
				 - 

## Inside Onyx by Michael Drogalis ##
-----

 - Ring - peer to peer
 - ZooKeeper 
 - May go to Gossip protocol

Fast full stack testing in om.next Jack Dubie
---------------------------------------------

 - Ladder = Life Insurance Compnay
 - real time underwrite risk with data
 
The Joys and Perils of Interactive Development Stuart Sierra
------------------------------------------------------------

 

## Untangled Web Development by Tony Kay ##


----------


 - Untangled framework on top of Om.next
 - Full stack framework
 - i18n
 - Concepts
	 - graph db format in client
	 - query/mutation notation
	 - data loading story: query normalize post-mutate to re-form in UI data views
	 - server-side query/mutation parsing /processing
 - UI Graph DB
	 - Ident = pointer kind of; identity of something 
 - React UI w/ Query
	 - query form the root
	 - structure ui db to match db
 - Idents help find things w/o traverse levels
 - Normalization done for you treeDb?  with Ident 
 - Immutable so data structure has same ref so cheap deltas
 - UI can be unit testable (css classes present)
 - Mutations
	 - trigger re renders
 - Problems Solved
	 - no controllers!
	 - UI query can be used to talk to server
	 - UI state history easy save/replay
 - Data loading
	 - load-data give the query
	 - post-mutate to make UI-concern structs
	 - :without //exclude fields
	 - Lazy Load
		 - load-feild this :item/comments
		 - figures out which query to use
 - Server
	 - Ring based
	 - Component Library - Stuart Sierra
		 - reset
	 - pluggable routes
 - Remote Mutations
	 - temp id on the ui -db fixed later 
	 - AST (assoc ast :params {:x 1}
		 - could be used to decorate requests like auth info
 - Testing
	 - no good fraemwork currently
	 - qa staff readable
	 - behavior driven
	 - clojurescript test based 
	 - Protocol Testing
		 - end 2 end
 - I18n
	 - gettext
 - Datomic Component
 - Support Viewer
	 - state in time 
	 - forward backward
	 - debug web apps - dvr like
	 - 


## Day 2 ##

## Announcements ##

Arachne Framework

 - Noobs are thrown in to the deep end
 - no high level framework like Rails
 - 

## Web Development is Distributed Systems Programming by Mikaela Patella ##

 - Yet Analytics
 - Evolution of web development
	 - websites to web apps
	 - front end backend 
	 - New paradigm: 
		 - Clojure atom - WebSockets channel/socket
		 - Scalability?
			 - take only delta of app state
			 - foundation of networked games 
				 - players, positions (states)
- web apps are always dist
- dist thinking already

Datalog all the way down Christopher Small
------------------------------------------

 - rewrite lead to simpler system
 - Datomic
 - [Re-frame microframework](https://github.com/Day8/re-frame)
	 - state
	 - central message log
	 - materialized views from Samza
	 - like Elm + Redux
	 - UI part of distributed system
	 - Eventual consistency
 - [Datascript](https://github.com/tonsky/datascript)
	 - datalog queries
	 - connecting post and reagent
		 - reactive datalog queries
 - Rest API is old
 - Seamless sync
 - broadcast tx data from master db
 - restrict via auth/scope
 - https://writings.quilt.org/2014/05/12/distributed-systems-and-the-end-of-the-api/
 - Datsync lib 
 - Sente web socket framework
 - Generic View Functions
	 - Slow non performant
 - REST APIs entangled with Auth Concerns
	 - Instead use streams of tx-data 
	 - Reactive filter db
		 - by eid
		 - by attribute
	 - Compose upstream of scope filters (auth)
	 - tx-forms whitelist pattern matching
	 - https://github.com/metasoarous/catalysis
	 - http://tonsky.me/blog/the-web-after-tomorrow/
	 - 

Caching half a billion user transactions Priyatam Mudivarti
-----------------------------------------------------------

 - Level Money Team
 - 32k Clojure
 - Python
 - Caches
	 - spatial locality
	 - temporal locality
		 - access data at certain time then you are likely to access data again at time +/- delta
	 - Concepts: Invalidation, Eviction, Naming
	 - Eviction - optimize space
	 - Evic + Invalidation - why coupled?
	 - Naming 
		 - {cache-name}-{uid}-{cache-version}-{logical-timestamp}
			 - keys come from timestamps
			 - auto invalidation for free
	 - Local Caching
		 - how to compute cache that is dependent on other caches (DAG)
			 - Invalidate one then invalidate rest
			 - hierarchical cache
			 - compile time check for circular dependencies
	 - Generational Cache
		 - immutable key tied to how values incache produced with dynamo db
		 - logical timestamps get atomically incremented to invalidate the cache
		 - queues invalidate every 24 hrs
		 - moved away to MemCach -> Dynamo 10K month
		 - race conditions - increment cache gen to inval cache
	 - Rmote cache (Memcache)
	 - Always Be Calibrating your approach to caching
	 - prefill user cache - precompute
	 - probs with memcache
		 - serial/deserial slow
	 - https://crossclj.info/clojure/compress.html
	 - Delays - Memoize done right
	 - Delay -> Future -> Promise
	 - Delay guaranteed to run only once and chaces the results the remaining
	 - Metrics.dropwizard.io
	 - Finally
		 - 600 lines of code for cache
		 - 1 protocol
		 - generational cache
		 - component with local a+ remote providers
		 - decoupled compute, serialize, deserialize
		 - 3 files

Hash Maps: more room at the bottom Peter Schuck
-----------------------------------------------
Hash Array Mapped Trie

 - bendyworks/lean-map ClojureScript
 - CHAMP perf gains
	 - iteration 2x
	 - eq check 
	 - trims hash maps
	 - code smaller
 - Sub node refs nil
	 - wiki metaphor
	 - get lost going down tree
 - optimize to know if KV meta 
 - no more nil
 - CHAMP iter thru kev pairs vs more complexity with current maps
 - 200x as long CHAMP vs regular hashmaps
	 - cuz delete algo 
	 - CHAMP Delete algo 
 - http://oai.cwi.nl/oai/asset/24029/24029B.pdf
 - overall hash maps much easier to understand and implement

CIDER: Inside the Brewery Bozhidar Batsov
-----------------------------------------
https://github.com/clojure-emacs/cider
 - works at Toptal 
 - @bhastov
 - 46% CIDER of mkt share
 - clojurescript
 - boot integration
 - enlighten from lighttable
 - auto injec CIDER dependencies
 - 0.12 
	 - suppress 
	 - breakpoints debugger
	 - step in while debugging
	 - test results improvements
	 - cider-auto-test-mode
	 - eldoc improvements
	 - docstring w/ mouse
	 - CIDER Hydras
		 - keybindings
	 - [Real Manual](https://cider.readthedocs.org/en/latest/)
	 - 

One Million Clicks per Minute with Kafka and Clojure Devon Peticolas
--------------------------------------------------------------------

Types are like the Weather, Type Systems are like Weathermen Matthias Felleisen
------------------------------------------------------------------------

- 
