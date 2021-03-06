Notes on all work being done in the pgsql branch for purposes of review by other developers.  This is __not__ a design specification that defines what/how the work will be done but rather an accounting of what has been done and why.  Highlight anything noteworthy here so others can review it at a high level and point out any problems they might think of that we've missed.

Organized by headers for specific problems, please be sure to update that section if the implemented solution evolves to something different.
# spacewalk-schema



Authors: dgoodwin

 * Included a hacky auto-generated schema from ora2pg. This is NOT what we will use in final product, only used for getting spacewalk-setup up and running.
 * See schema/spacewalk/postgresql/postgresql.universe.satellite.sql
 * This is a flat file just included directly in the schema.spec.
 * Section will be updated when more final changes to the schema package are made.
# spacewalk-setup



Authors: dgoodwin

 * Refactored to isolate Oracle specific methods.
   * No clean object oriented way to do this with Perl so renamed methods to include "oracle" in the name if they are Oracle specific, grouped them together as much as possible (may extract to a module someday), and added primary entry point. (look for oracle_setup_db method)
   * Done in master as this code is not technically PostgreSQL specific and involves heavy refactoring, which is a recipe for merge conflicts that don't need to be dealt with.
   * Added "db-backend" option. Support value of "oracle" in master, if nothing is specified this is the default. "postgresql" is an option in the pgsql branch.
 * Get spacewalk-setup installing PostgreSQL schema.
   * Using poorly autogenerated schema based on ora2pg output. All it has to do is load. See schema/spacewalk/postgresql/postgresql.universe.satellite.sql.
   * Started working to achieve this goal assuming the admin has already setup an existing PostgreSQL database.
   * "Embedded" installations where PostgreSQL is installed and setup for you will come later.
   * See oracle_setup_db and postgresql_setup_db in spacewalk/setup/lib/Spacewalk/Setup.pm. These are the primary entry points for going down a path to apply either version of the schema.
   * Added a Requires for perl-DBD-Pg to spacewalk-setup.spec so we can communicate with PostgreSQL from Perl.
   * Successfully was able to install the ora2pg generated schema and move in through spacewalk-setup until the time came to try Satellite activation.
   * Hit problem with rhnSQL in Python.
# rhnSQL Python Driver



Authors: dgoodwin

  * While Python DBAPI defines a common API for database access, the drivers sometimes go there own way, Oracle driver does and in our case we *are* using some of these customizations.
  * rhnSQL authors saw fit to introduce our own database "driver" abstraction on top of DBAPI, originally to support two versions of Oracle drivers. I honestly can't tell if this has helped or hurt us, it may have helped quite a bit due to previous point.
  * Despite this driver abstraction, the implementation and class structure remained ridiculously Oracle specific and required another heavy refactor.
  * Did *ALL* this in master thinking that the PostgreSQL driver is completely unused and just sits there. Now contemplating moving this into the pgsql branch however and leaving the Oracle refactor in master.
  * Currently when I last left off, PostgreSQL rhnsql driver is mostly complete but still lacks ability to call procedures, as well as a LOT of testing.
  * Ran into empty_blob() function in Oracle. PostgreSQL blob columns appear to be ported as bytea's, chose to get past this by adding a PostgreSQL function of the same name that just returns an empty bytea.
  * Blob population in Oracle issue (select column for as, followed by explicit write in the Python code). To workaround introduced rhnSQL.Cursor.update_blob() function which abstracts the weirdness required for Oracle blobs, but just does a normal update for the PostgreSQL column.
# ORDER BY ... NULLS {FIRST|LAST}

  * This is a syntax that is provided by Oracle, but available in Postgres only since version 8.3. So we need a way to port such queries to Postgres version 8.1 and 8.2.

    By default, Postgres treats NULLs as larger than any other data; so following behaviour is noticed:

    postgres=# select * from t order by a;
       a    
    --------
          1
          2
     <null>
    (3 rows)
    
    postgres=# select * from t order by a DESC;
       a    
    --------
     <null>
          2
          1
    (3 rows)

    The following solution has been agreed upon in the mailing list. The problem query was using 


    ORDER BY version NULLS LAST

    and it has been successfully replaced with:


    order by CASE WHEN version IS NULL
                  THEN 1
                  ELSE 0
             END, version
# rhn-ssl-dbstore script



 * Script used to store generated SSL cert in the database. Accepts a --db=user/pass@sid option.
 * --db obviously needs to go, question is should it be removed or replaced with all --db-user --db-pass --db-host --db-name --db-port etc.
   * Usage seems rare, removing for now.