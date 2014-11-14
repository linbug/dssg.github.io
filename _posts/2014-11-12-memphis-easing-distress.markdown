---
layout: post
title: "Easing the Distress of Neighborhoods with Data"
author: Tom Plagge
pic: "memphis-sign.jpg"
---

<img src="/img/posts/memphis-team.png">

[<a href="http://dssg.uchicago.edu/faq/">Apply today for DSSG 2015!</a>]

Rapid growth, followed by a period of suburbanization and instability, ending with a gradual rebirth of the core: that’s the last hundred years in a nutshell for most American cities. But the recent wave of urban reinvestment has been uneven, especially since the financial meltdown of 2008. Some central city neighborhoods have rapidly revitalized, while others have continued to struggle with problems like foreclosures, crime, and joblessness. There are many complex and interrelated issues faced by such areas, but in the City of Memphis, a group of stakeholders has crystallized around one issue in particular: blighted properties.

We define blight as distressed properties in need of structural or major cosmetic repairs in order to be brought up to the community’s standards. Blight is a symptom of economic malaise, but potentially a cause as well, since its presence sends negative signals to potential investors and residents. When you talk to community leaders in Memphis, it’s inevitably one of the first issues they raise. So what can be done about it, and what role can data play?

####The Lay of the Land

No two of Memphis’s neighborhoods are the same, but for those of us who are from elsewhere, categorizing them by economic characteristics can help build an intuition for how the city works. We used clustering algorithms to categorize the census tracts in Shelby County (which includes Memphis) using economic features like unemployment, poverty, and income; and real estate data like residential and commercial value per square foot, property age, taxes paid per property, percentage of foreclosures, and blighted properties. 

The pink areas in the map below are the city’s most prosperous, with high incomes and low levels of blight -- that’s downtown Memphis, parts of the vibrant Midtown neighborhood, and the more recently developed outskirts. The blue areas are those facing the biggest challenges -- if you squint, you can see why Memphians sometimes refer to the “C” of poverty (it's really a backwards "C"). The yellow areas are somewhere in between. 

<img src="/img/posts/memphis-economy.png">

####Distilling Data for Diagnosis

One of the first things we noticed while talking to stakeholders in Memphis was the immense practical challenge of identifying which properties are distressed. The City and community groups have limited budgets to spend on neighborhood improvements, so it’s vital to know the scale of the problem as a function of both space (which neighborhoods are worse off?) and time (where are things getting better or worse?).

The most straightforward way to obtain property condition information is by sending surveyors out into the city. Back in 2008, a group called CBANA at the University of Memphis did exactly that, marshalling a small army of volunteers and researchers to inspect every single property. Given the time and resources required to accomplish this feat, Memphis has no plans to repeat it in the immediate future. 

In an ideal world, though, they wouldn’t have to -- the local government is already in possession of vast quantities of administrative data that gets updated as a part of the city’s normal workflow. If this data can be accurately distilled down to a simple indicator of distress for each parcel, the resources dedicated to surveying properties can be spent elsewhere.

Using the CBANA survey as a training set, we took administrative data collected regularly by the city and county -- tax assessments and foreclosures, for example -- and built a random forest classifier to identify properties as “distressed” or “not distressed” in the years 2011, 2012, and 2013. The model produces a probability that each property is in each category, which we can use to obtain a more nuanced estimate of a property’s condition than a simple binary distinction. We turned these results into a web application that displays a color-coded risk assessment for every residential property in Memphis, which you can see below. Notice that the highest-risk areas correspond to the blue parts of the classifier map, as you might expect.

<img src="/img/posts/memphis-distressed.png">

We imagine this as a useful tool for city officials and community organizations to identify where they should invest in revitalization projects. While our estimates are not perfect, they will provide entry points for targeted evaluations of specific properties to determine where to invest. Furthermore, we can update the model to create more recent estimates each year when new data becomes available. We hope that future work can extend our model into prediction, which could help residents and policymakers intervene in at-risk properties before they become distressed.

####Estimating The Value of Rehabilitation

Memphis is fortunate to have a number of excellent organizations working to fight blight. Among the most active are Community Development Corporations. CDCs often purchase or are granted ownership of dilapidated houses in a certain neighborhood, and work to restore them and sell them to community members who will reside in and maintain them. This both keeps houses from falling into further disrepair, and provides high-quality homes to people who need them.
 
The cost of acquisition and renovation typically exceeds the market value of the house, often by as much as $20,000. However, the sale price of the house fails to capture all of the impact of a restoration on the total neighborhood tax revenues. The hope is that, if the full tax increment of the rehabilitation is known, it will be easier for CDCs to close the $20,000 gap through government or foundation funding. 

To estimate the impact of a rehabilitation on neighborhood property values, we first attempted to reverse-engineer the model used by the county tax assessor. We matched each house in a neighborhood with the five recent sales most similar to it in size, age, and quality. We then adjusted the sales price of the comparables using coefficients from a hedonic regression of house prices, and averaged them to get an estimate of the total assessed value. By varying the inclusion or exclusion and sale price of a specific property, we can see what the impact of a hypothetical renovation might be.
 
We used this as the basis for another web application that allows policymakers to explore the potential impacts of renovating various houses in the neighborhood. Tax increment financing is common across the country (including, here in Chicago) as a funding method for affordable housing and other neighborhood improvements. Our application can easily be extended to additional jurisdictions so long as the administrative data is available.

<img src="/img/posts/memphis-rehab.png">

####To Rehab or Not To Rehab

While the effect of remediation on tax revenues is important, we were also curious about how it might affect the neighbors. To figure it out, we applied a causal inference technique called propensity score matching in an attempt to determine conclusively whether rehabilitating distressed properties has an effect on the sale values of properties around them. 

Since we didn’t have experimental data, we needed to take care not to confuse cause and effect--it’s certainly the case that rehabilitation is more common in areas with rising property values, but that doesn’t mean that the rehabilitations are causing that rise. Our method addresses this by pairing every property near a rehabilitation with a property that is otherwise similar but where no neighboring home was rehabbed (shown in the map below). Think of it as constructing an <i>ex post facto</i> treatment and control group.

<img src="/img/posts/memphis-rehab-values.png">

We first picked out all properties that had been identified as distressed in the CBANA survey and had subsequently seen non-demolition building permits totaling more than $10,000. We then identified property sales within 500 feet of these rehabilitations. We paired each of these sales with the sale of an otherwise-similar home in a similar, nearby neighborhood that was not near any rehabbed property.

Unfortunately, the results were inconclusive. We were not able to find a clear effect of rehabilitation on property values, but we also were not able to show that there is not an effect (i.e., we found results that were positive but not statistically significant). Property markets are tough to model: houses sell very infrequently -- there were only 200 sales in Memphis around rehabilitated properties over the course of the years in question -- and no two houses are exactly alike. What we’d like to be able to say is “rehabilitation seems to raise property values for these kinds of homes in these neighborhoods, but demolition is more practical for those other homes, and the rest can just be left alone.” But that requires more data.

####Conclusion

Things are looking up in Memphis. The core of the city is as vibrant as it has been in decades, and community groups are hard at work bringing stability and investment to the areas that need it most. But there’s no one right answer for how to revitalize a struggling neighborhood, and resources, as always, are tight. We built some useful tools to help policy makers and community groups direct their energies to the most promising properties, and we laid the groundwork for evaluating the economic impact of their work. But this is just the first step. As the fight against blight continues, we look forward to seeing the measurable impact their strategies have on the quality-of-life and economic well-being of Memphians.

####Acknowledgements

None of our work would have been possible without the in-depth discussions and data provided by:

<ul>
<li>Tommy Pacello, Abby Miller, and George Lord from the Mayor’s Innovation Delivery Team.</li>
<li>Nate Ferguson and Tk Buchanan at the University of Memphis.</li>
<li>The Binghampton and Frayser CDCs.</li>
<li>Rick Stieg and many others in Memphis & Shelby County.</li>
</ul>
