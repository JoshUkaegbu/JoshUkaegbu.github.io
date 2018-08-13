---
title: "Python Engineering Project: Single Degree of Freedom Elastic System Analysis"
date: 2018-08-11
tags: [singe degree of freedom, elastic system, mechanical engineering]
header:
  image: "images/single-degree/single-degree.jpg"
---

This is a program I created when on secondment at a engineering consultancy.
The program calculates the displacement, and acceleration of a single degree of
freedom mass damper system. It then automatically generates a graph of time vs
displacement.

This program really streamlined the process of solving these difficult
differential equations that must be solved for this engineering analysis.

The success of this program is that it encouraged my whole team to learn more
about programming.

Python code block:
```python
import math
import numpy as np
import matplotlib.pyplot as plt

# creating input parameters

mass = float(input("Please enter a mass value (kg)"))
stiffness = float(input("Please enter a stiffness value"))
time = float(input("Please enter a value for time (s)"))

deltaT = ((mass/stiffness) * (math.pi * 2.0))/10.0


print("deltaT = {}".format(deltaT))

r = np.arange(0, time, deltaT)

# creating the empty arrays that will store the outputs

forceArray = np.array([])
timeArray = np.array([])
accArray = np.array([0, 0])
disArray = np.array([0])

# for loop to generate an array of force values for different time steps

for t in r:
if 0 <= t < 0.1:
    Force = (500 * t) + 50
elif 0.1 <= t < 0.14:
    Force = (-1250 * t) + 225
else:
    Force = 50

forceArray = np.append(forceArray, [Force])


print("forceArray = {}".format(forceArray))

# for loop to generate array of different time values

for t in r:
timeArray = np.append(timeArray, [t])

print("timeArray = {}".format(timeArray))

y0 = 0

a0 = (forceArray[0] - (stiffness * y0))/ mass

accArray = np.append(accArray, [a0])

y1 = (a0/2) * (deltaT**2)

disArray = np.array([y0, y1])

r2 = np.arange(2, (len(forceArray)-1))

# for loop to generate the array of acceleration, and finally displacement values

for i in r2:
acceleration = ((forceArray[i-1]) - (stiffness * disArray[i-1]))/ mass
accArray = np.append(accArray, [acceleration])

displacement = ((2 * disArray[i-1]) - (disArray[i-2]) + (accArray[i-1] * (deltaT **2)))
disArray = np.append(disArray, [displacement])

print("Acceleration = {}".format(accArray))    
print("Displacement = {}".format(disArray))

# Using matplotlib to plot a graph of time vs displacement for a single degree of freedom system

x = np.array([timeArray])
y = np.array([disArray])
plt.plot(x, y)

plt.xlabel("Time")
plt.ylabel("Displacement")

plt.savefig('plot.png')
plt.show()
```
