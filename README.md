# SQL
MYSQL_Indianpopulation
use indian_population;
SELECT * FROM dataset1;

-- add Another column as ID for both of table

alter table dataset1
add column ID int not null auto_increment first,
add primary key(id);

alter table indian_population.dataset2
add column id int not null,
add foreign key(id) references indian_population.dataset1;

select District as Districtgrowth, state from indian_population.dataset1
where Growth > 35 limit 5;

-- Number of raw in data set

select count(*) from dataset1;

-- my state 
select * from dataset1 
where State = 'Gujarat';

-- Neighbouring state of Gujarat with > 80 literacy, sex_ratio 

select District, state, Literacy, sex_ratio from indian_population.dataset1
where state in ('Rajasthan','Gujarat','Maharashtra','Madhya Pradesh') 
and literacy > 80;

-- avg growth

select state, round(avg(growth),2) as Avg_Growth from 
dataset1 group by State;

-- avg sex ratio

select state, round(avg(sex_ratio),0) 
as Avgsex_ratio 
from dataset1 
group by state 
order by  Avgsex_ratio desc; 

-- avg literacy rate > 90 only

select state, round(avg(literacy),0) 
as AvgLiteracy 
from indian_population.dataset1
group by State
having round(avg(literacy),0)>90
order by Avgliteracy desc;

-- Top 5 of Growing State 

select state , round(avg(Growth),0) as Avg_growth
from dataset1
group by state
order by Avg_growth desc limit 10;

-- Bottom  10 District with Literacy rate
select District, State, Literacy from dataset1
order by Literacy limit 10;

-- top and bottom 3 states in literacy state

drop table if exists topstates;
create table topstates
( state varchar(255),
  topstate float
);
insert into topstates
select State,round(avg(literacy),0)
as Avg_literacy_ratio 
from dataset1 
group by State 
order by Avg_literacy_ratio
desc;

select * from topstates 
order by topstate desc  limit 3;

select * from topstates;

-- bottom state by literacy
drop table if exists Bottomstates;
create table Bottomstates(
state varchar(255),
Bottomscore float
);
insert into Bottomstates
select state,
round(avg(literacy),0) 
as Bottomscore
from indian_population.dataset1
group by state
order by Bottomscore 
desc;
select * from bottomstates;

select * from bottomstates 
order by bottomstates.bottomscore asc
limit 3;

select * from dataset2;

-- Start A and B

select distinct state from dataset1 
where state like 'a%' 
or  state like 'b%';

-- Retaltion of Literacy and sex ratio with Population and area
select * from dataset2
where state in (select  state from dataset1
where  literacy > 90 and Sex_Ratio > 950); 

-- show index
show index from dataset1;

-- inner join with Two table
select a.District, b.population from dataset1 a
inner join dataset2 b
on a.ID=b.ID where a.district='Patan';

-- Right join with Two table
select a.district, a.Growth, b.population from dataset1 a
right join dataset2 b
on a.id=b.id where
a.District ='Banaskantha';

-- total males and females
select d.state,sum(d.males) total_males,sum(d.females) total_females from
(select c.district,c.state state,round(c.population/(c.sex_ratio+1),0) males,
round((c.population*c.sex_ratio)/(c.sex_ratio+1),0) females 
from(select a.district,a.state,a.sex_ratio/1000 sex_ratio,b.population
from dataset1 a 
inner join dataset2 b 
on a.district=b.district) c) d 
group by d.state;

select * from dataset2;
select * from dataset1;

-- Total literacy number

select d.state, 
sum(L_Number) as Literate_Number, 
sum(Iliterate) as IL_number
from 
(select c.district, c.state ,
round(c.L_ratio*c.population,0) as L_Number, 
round((1-c.L_ratio)*c.population,0) as Iliterate, c.population
from (select a.District, a.State, a.Literacy/100 as L_ratio, b.Population
from dataset1 a
inner join dataset2 b
on a.ID=b.ID)c)d group by d.state;


-- population in previous census

select 
sum(o.previous_census) as PreviousCensus,
sum(o.Current_census) as CTotal
from
(select d.state, 
sum(previous_census) as previous_census,
sum(PTotal) as Current_census 
from
(select c.state, c.district, 
round(population/(1+growth/100),0) as previous_census,
c.Population PTotal
from 
(select a.district,a.state,a.growth ,b.population 
from dataset1 a 
inner join dataset2 b 
on a.id=b.id)c)d
group by d.state)O;

-- output top 3 districts from each state with highest literacy rate


select a.* from
(select district,state,literacy,
rank() over(partition by state order by literacy desc) 
rnk from dataset1) a
where a.rnk in (1,2,3) order by state;

select a.* from (select District, State, Literacy, 
rank() over (partition by state Order by literacy desc) as  Rankk
from dataset1)a 
where a.rankk in (1,2,3) order by state;




