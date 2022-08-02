
-- 1. Calculating total death percentage 

Select SUM(new_cases) as total_cases, SUM(cast(new_deaths as unsigned)) as total_deaths, SUM(cast(new_deaths as unsigned))/SUM(New_Cases)*100 as DeathPercentage
From covidpro.death
where continent is not null 
order by 1,2

-- 2. Calculating total death count by continent

Select continent, SUM(cast(new_deaths as unsigned)) as TotalDeathCount
From covidpro.death
Where continent is not null
Group by continent
order by TotalDeathCount desc;


-- 3. Calculating % of population infected by country

Select Location, Population, MAX(total_cases) as HighestInfectionCount,  Max((total_cases/population))*100 as PercentPopulationInfected
From covidpro.death
Group by Location, Population
order by PercentPopulationInfected desc


-- 4. Calculating % of population infected by country over years

Select Location, Population, date, MAX(total_cases) as HighestInfectionCount,  Max((total_cases/population))*100 as PercentPopulationInfected
From covidpro.death
Group by Location, Population, date
order by PercentPopulationInfected desc

-- 5. people vaccinated against population of the country by date

Select dea.continent, dea.location, dea.date, dea.population, MAX(vac.total_vaccinations) as People_Vaccinated
From covidpro.death dea
Join covidpro.vacc vac
	On dea.location = vac.location
	and dea.date = vac.date
where dea.continent is not null 
group by dea.continent, dea.location, dea.date, dea.population
order by 1,2,3

-- 6. vaccinated people all over the world over years

With PopvsVac (Continent, Location, Date, Population, New_Vaccinations, People_Vaccinated)
as
(
Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations, SUM(CAST(vac.new_vaccinations as unsigned)) OVER (Partition by dea.Location Order by dea.location, dea.Date) as People_Vaccinated
From covidpro.death dea
Join covidpro.vacc vac
	On dea.location = vac.location
	and dea.date = vac.date
where dea.continent is not null
)
Select *, (People_Vaccinated/Population)*100 as Percent_People_Vaccinated
From PopvsVac

-- 7. Calculating total cases vs total death over years in India

Select Location, date, population, total_cases, total_deaths
From covidpro.death
Where location like 'India'
and continent is not null 
order by 1,2

-- 8. Calculating % of population infected in India over years

Select Location, Population,date, MAX(total_cases) as HighestInfectionCount,  Max((total_cases/population))*100 as PercentPopulationInfected
From covidpro.death
Where location like 'India'
Group by Location, Population, date
order by PercentPopulationInfected desc

-- 9. calculating how India, USA and China tackled vaccination requirement/need against their healthcare budget
-- extracting yearly vaccinations for India, USA and china
-- extracted seperate data in excel files
-- created pivot table and grouped by year to get data from 2020, 2021, and 2022


