create database demo;

create table emp(
	emp_id serial primary key,
	emp_name varchar (30),
	address varchar (50),
	isactive bool
);

select * from emp;
-----------------------------------------------------------------------------------------------------------------------------
create or replace function emp_details(emp_name varchar(30),address varchar(50),isactive bool)
returns table (value_ integer,text_ varchar) 
language plpgsql
as $$
begin

   	insert into emp(emp_name, address,isactive) values(emp_name,address,isactive);
    return query
    select 1::INTEGER as value_ ,'1 ROW INSERTED SUCCESSFULLY'::VARCHAR as text_;
	
end;
$$
;

select * from emp_details('mahi','kollapur',0::bool);
---------------------------------------------------------------------------------------------
create or replace function get_emp_details()
returns table(emp_id int,emp_name varchar,address varchar,isactive bool)
language plpgsql 
as $$
begin
	return query
	select e.emp_id,e.emp_name,e.address,e.isactive from emp e ;
	
end;
$$
;

select * from  get_emp_details(3);
----------------------------------------------------------------------------------------------------------------------

create or replace function emp_getdata(_emp_id int, _emp_name varchar(30),_address varchar(50),_isactive bool)
returns table(value_ integer,text_ varchar)
language plpgsql
as $$
begin

if(_emp_id=0 and _isactive=1::bool) then
	
		insert into emp(emp_name,address)values(_emp_name,_address);
		
		return query
		select 1::INTEGER as value_ ,' ROW INSERTED SUCCESSFULLY..'::VARCHAR as text_;

elseif(_emp_id  !=0 and _isactive=1::bool) then
		update emp set emp_name=_emp_name,address=_address
		where emp_id=_emp_id;
		
		return query
		select 1::INTEGER as value_ ,'ROW UPDATED SUCCESSFULLY..'::VARCHAR as text_;
		
elseif(_emp_id !=0 and _isactive=0::bool) then

		update emp set isactive=0::bool
		where emp_id=_emp_id ;
		
		return query
		select 1::INTEGER as value_ ,'ROW UPDATE SUCCESSFULLY..'::VARCHAR as text_;
END IF;  
END;
$$;

select * from emp_getdata(0,'kavita','bangalore',1::bool);

select * from emp_getdata(9,'amar','bangalore',1::bool);

select * from emp_getdata(9,'amar','bangalore',0::bool);

select * from emp;
delete from emp where emp_id=8;
------------------------------------------------------------------------------------------------------------------------
create or replace function get_emp_TrueData(_emp_id int)
returns table(value_ integer,text_ varchar,text__ varchar,status_ bool)
language plpgsql 
as $$
begin
if(_emp_id is NULL) then
	return query
	select e.emp_id,e.emp_name,e.address,e.isactive from emp e
	where e.isactive=1::bool;
		
elseif(_emp_id is NOT NULL) then	
	return query
	select e.emp_id,e.emp_name,e.address,e.isactive  from emp e
	where e.emp_id=_emp_id and e.isactive=1::bool;
	
END IF;
end;
$$
;

drop function get_emp_TrueData(_emp_id int);

select * from emp;
select * from get_emp_TrueData(null);
select * from get_emp_TrueData(1);
select * from get_emp_TrueData(2);
