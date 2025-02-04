# JPA

```java
@Entity
public class Creator implements Serializable {
    @Id
    @GeneratedValue(strategy=GenerationType.IDENTITY)
    private int uid;
    
    private String firstname;
    private String lastname;
    private String username;
    private String password;
    private byte[] photo;
    private String status;
    
    @OneToMany(mappedBy="author", fetch=FetchType.EAGER)
    @OrderBy("creationDate DESC")
    private List<Content> authoredContents;
    
    @ManyToMany(mappedBy="likers", fetch=FetchType.LAZY)
    private List<Content> likedContents;
}
```

```java
@Entity
public class Content implements Serializable {
    @Id
    @GeneratedValue(strategy=GenerationType.IDENTITY)
    private int cid;
    private String title;
    private String description;
    @Temporal(TemporalType.DATE)
    private Date creationDate;
    private byte[] mediafile;
    
    @ManyToOne
    @JoinColumn(name="author")
    private Creator author; // owner
    
    @ManyToMany
    @JoinTable(
        name="liking",
        joinColumns={@JoinColumn(name="cid")},
        inverseJoinColumns={@JoinColumn(name="uid")}
    )
    @OrderBy("lastname DESC")
    private List<Creator> likers; // owner
    
    @ManyToMany(mappedBy="items", fetch=FetchType.EAGER)
    private List<Category> categories;
}
```

```java
@Entity
public class Category implements Serializable {
    @Id
    @GeneratedValue(strategy=GenerationType.IDENTITY)
    private int catid;
    private String name;
    
    @ManyToMany(fetch=FetchType.LAZY)
    @JoinTable(
        name="genres",
        joinColumns={@JoinColumn(name="catid")},
        inverseJoinColumns={@JoinColumn(name="cid")}
    )
    @OrderBy("creationDate DESC")
    private List<Content> items; // owner
}
```





```java
@Entity
public class REauction implements Serializable {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private int REauctionId;
    private String property;
    @Temporal(TemporalType.DATE)
    private Date startDate;
    private String description;
    private float initialValue;
    private float minIncrement;
    
    @OneToMany(mappedBy="REauction", fetch=FetchType.EAGER, cascade=CascadeType.REMOVE)
    private List<Bidder> bidders;
}
```

```java
@Entity
public class Bidder implements Serializable {
    @Id
    @GeneratedValue(strategy=GenerationType.AUTO)
    private int BidderId;
    private String name;
    private float maxAmount;
    
    @ManyToOne(fetch=FetchType.EAGER)
    @JoinColumn(name="REauction")
    private REauction REauction;
    
    @OneToMany(mappedBy="bidder", fetch=FetchType.EAGER, cascade=CascadeType.REMOVE)
    @OrderedBy("when DESC")
    private List<Bid> bids;
}
```

```java
@Entity
public class Bid implements Serializable {
    @Id
    @GeneratedType(strategy=GenerationType.AUTO)
    private int bidId;
    private float amount;
    @Temporal(TemporalType.DATE)
    private Date when;
    
    @ManyToOne(fetch=FetchType.EAGER)
    @JoinColumn(name="bidder")
    private Bidder bidder;
}
```





# Trigger

 ```sql
 STATION(SID, name, status)
 POLLUTANT(PID, name, threshold)
 RECORD(SID, PID, TIMESTAMP, value)
 STATS(SID, PID, average, peak)
 ALERT(SID, TIMESTAMP)
 
 CREATE TRIGGER T1
 AFTER INSERT ON RECORD
 FOR EACH ROW
 
 BEGIN
 	UPDATE STATS
 	SET AVERAGE = (
         -- we can use AVG just simply like this 
     	SELECT AVG(value) 
         FROM RECORD
         WHERE PID = NEW.PID AND SID = NEW.SID
     )
     WHERE SID = NEW.SID AND PID = NEW.PID;
 END;
 
 ----------------------------------------------------
 STATION(SID, name, status)
 POLLUTANT(PID, name, threshold)
 RECORD(SID, PID, TIMESTAMP, value)
 STATS(SID, PID, average, peak)
 ALERT(SID, TIMESTAMP)
 
 CREATE TRIGGER T2
 AFTER INSERT ON RECORD
 FOR EACH ROW
 
 BEGIN
 IF NEW.value > (
 	SELECT peak 
     FROM STATS 
     WHERE SID = NEW.SID AND PID = NEW.PID;
 )
 THEN
 	UPDATE STATS
 	SET peak = NEW.value
 	WHERE SID = NEW.PID AND PID = NEW.PID;
 END IF;
 END;
 
 ----------------------------------------------------
 STATION(SID, name, status)
 POLLUTANT(PID, name, threshold)
 RECORD(SID, PID, TIMESTAMP, value)
 STATS(SID, PID, average, peak)
 ALERT(SID, TIMESTAMP)
 
 CREATE TRIGGER T3
 AFTER UPDATE OF peak ON STATS
 FOR EACH ROW
 
 DECLARE MAX_TS INT;
 
 BEGIN
 	SELECT max(timestamp) INTO MAX_TS 
 	FROM RECORD
 	WHERE SID = NEW.SID;
 	
 	IF NEW.peak > (
     	SELECT threshold 
         FROM POLLUTANT 
         WHERE PID = NEW.PID;
     )
     THEN
     	UPDATE STATION
     	SET status = 'critical'
     	WHERE SID = NEW.SID;
     	
     	IF EXISTS (SELECT * FROM ALERT WHERE SID = NEW.SID)
     	THEN
     		UPDATE ALERT
     		SET timestamp = MAX_TS
     		WHERE SID = NEW.SID;
     	ELSE
     		INSERT INTO ALERT(SID, timestamp)
     		VALUES(NEW.SID, MAX_TS);
     	END IF;
     END IF;
 END;
 
 ----------------------------------------------------
 STATION(SID, name, status)
 POLLUTANT(PID, name, threshold)
 RECORD(SID, PID, TIMESTAMP, value)
 STATS(SID, PID, average, peak)
 ALERT(SID, TIMESTAMP)
 
 CREATE TRIGGER T4
 AFTER INSERT ON RECORD
 FOR EACH ROW
 WHEN (SELECT STATUS FROM STATION WHERE SID = NEW.SID) = 'critical'
 
 DECLARE ALERT_TS INT;
 
 BEGIN
 	SELECT TIMESTAMP INTO ALERT_TS
 	FROM ALERT
 	WHERE ALERT.SID = NEW.SID;
 	
 	IF
 		(SELECT COUNT(*)
 		FROM RECORD 
 		JOIN POLLUTANT ON RECORD.SID = NEW.SID AND RECORD.PID = POLLUTANT.PID
 		WHERE TIMESTAMP > ALERT_TS AND value < threshold) > 10
 	THEN
 		UPDATE STATION
 		SET STATUS = 'normal'
 		WHERE SID = NEW.SID;
 		
 		DELETE FROM ALERT 
 		WHERE SID = NEW.SID;
 	END IF;
 END;
 ```





```sql
TRUCK(PLATE, status, ownerId) -- status: available (default), maintenance
DRIVER(ID, hoursTravelled, status) -- status: authorized (default), non-authorized
                                  -- hoursTravelled defaults to 0
TRIP(TRIPID, date, origin, destination, truckPlate, driverId)
TRIPREPORT(TRIPID, hoursTravelled)
ALTERNATE(DRIVERID, ALTERNATEDRIVERID) -- DRIVERID is the owner

CREATE TRIGGER CheckMaintenance
BEFORE UPDATE OF truckPlate ON TRIP
FOR EACH ROW
BEGIN
	DECLARE truck_status VARCHAR(20);
	
	SELECT status INTO truck_status
	FROM TRUCK
	WHERE PLATE = NEW.truckPlate;
	
	IF truck_status = "maintenance"
	THEN
		SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = "some error occurred";
	END;
END;

CREATE TRIGGER AssignDriver
AFTER UPDATE OF truckPlate ON TRIP
FOR EACH ROW
BEGIN
	
END;
```



```sql
MOVIE (MOVIEID, title, duration, budget)
PERSON (PID, name, total_salary)
PERFORMANCE (MOVIEID, PID, ROLE, salary) -- performer PID played ROLE in movie MOVIEID
RICHEST (PID)
MOST_VERSATILE (PID, MOVIEID, number_of_roles)

CREATE TRIGGER T1
AFTER INSERT ON PERFORMANCE
FOR EACH ROW
BEGIN
	IF
		(SELECT SUM(salary) 
		FROM PERFORMANCE 
		WHERE MOVIEID = NEW.MOVIEID) > (
        	SELECT budget
            FROM MOVIE
            WHERE MOVIREID = NEW.MOVIEID
        )
	THEN
		RAISE EXCEPTION 'Budget exceeded';
	END IF;
END;
-------------------------------------------
MOVIE (MOVIEID, title, duration, budget)
PERSON (PID, name, total_salary)
PERFORMANCE (MOVIEID, PID, ROLE, salary) -- performer PID played ROLE in movie MOVIEID
RICHEST (PID)
MOST_VERSATILE (PID, MOVIEID, number_of_roles)

CREATE TRIGGER T2
AFTER INSERT ON PERFORMACE
FOR EACH ROW
BEGIN
	UPDATE PERSON
	SET total_salary = total_salary + NEW.salary
	WHERE PID = NEW.PID;
END;
-------------------------------------------
MOVIE (MOVIEID, title, duration, budget)
PERSON (PID, name, total_salary)
PERFORMANCE (MOVIEID, PID, ROLE, salary) -- performer PID played ROLE in movie MOVIEID
RICHEST (PID)
MOST_VERSATILE (PID, MOVIEID, number_of_roles)

CREATE TRIGGER T3
AFTER UPDATE OF total_salary ON PERSON
FOR EACH ROW
DECLARE
    pid int;
    tot int;
BEGIN
    SELECT P.PID, P.total_salary INTO pid, tot
    FROM PERSON P, RICHEST R
    WHERE P.PID = R.PID;

    IF pid IS NULL
    THEN
        INSERT INTO RICHEST(PID)
        VALUE(NEW.PID);
    ELSE
        IF tot < NEW.total_salary
        THEN
            UPDATE RICHEST
            SET PID = NEW.PID;
        END IF;
    END IF;
END;
-------------------------------------------
MOVIE (MOVIEID, title, duration, budget)
PERSON (PID, name, total_salary)
PERFORMANCE (MOVIEID, PID, ROLE, salary) -- performer PID played ROLE in movie MOVIEID
RICHEST (PID)
MOST_VERSATILE (PID, MOVIEID, number_of_roles)

CREATE TRIGGER T4
AFTER INSERT ON PERFORMACE
FOR EACH ROW
DECLARE 
	roles int;
BEGIN
	SELECT COUNT(*) INTO roles
	FROM PERFORMANCE
	WHREE PID = NEW.PID AND MOVIEID = NEW.MOVIEID;
	
	IF
    	-- not yet 5 performers in the top-5
    	(SELECT COUNT(*)
    	FROM MOST_VERSATILE) < 5
    	OR
    	-- at least as many roles as the current top-5
    	roles > (
        	SELECT number_of_roles
            FROM MOST_VERSATITLE
            ORDER BY number_of_roles
            FETCH FIRST 1 ROW ONLY;
        )
	THEN
		-- remove the entry for that PID, MOVIEID, if it exists
		DELETE FROM MOST_VERSATILE
		WHERE PID = NEW.PID AND MOVIEID = NEW.MOVIEID;
		
		-- insert the pair in the top-5 list
		INSERT INTO MOST_VERSATILE(PID, MOVIEID, number_of_roles)
		VALUE(NEW.PID, NEW.MOVIEID, roles);
		
		-- remove those whose number_of_roles is worse than that of more than 4 performers
		DELETE FROM MOST_VERSATILE A
		WHERE 4 < (
        	SELECT COUNT(*)
            FROM MOST_VERSATILE B
            WHERE B.number_of_roles > A.number_of_roles
        );
	END IF;
END;
```

