# COVID-19-Analysis
Coronavirus disease 2019 (COVID-19) is an infectious disease caused by severe acute respiratory syndrome coronavirus 2 (SARS-CoV-2).The disease was first identified in December 2019 in Wuhan, the capital of China's Hubei province, and has since spread globally, resulting in the ongoing 2019–20 coronavirus pandemic

This project ultimate objective focuses on understanding the COVID19 patterns in the most affected countries in the world, mainly USA and Italy, Spain, Germany, France and UK from Europe.

I tried to implement the SIR Modeling by setting an initial population that could have had been exposed to Covid 19 by 25th March which was declared as the lockdown in India. 

The SIR model is a simple mathematical description of the spread of a disease in a population is the so-called SIR model, which divides the (fixed) population of N individuals into three "compartments" which may vary as a function of time, t:

      S(t) are those susceptible but not yet infected with the disease;
      I(t) is the number of infectious individuals;
      R(t) are those individuals who have recovered from the disease and now have immunity to it.

      dSdt = −βSI/N
      dIdt = βSI/N−γI 
      dRdt = γI
      
      β = contact rate (R0/γ); R0 = the avg. no. of people who can be infected by a positive Covid patient 
      γ = 1/no. of days to recover

For more information you can refer to the documentation here: https://scipython.com/book/chapter-8-scipy/additional-examples/the-sir-epidemic-model/

For the estimation of the parameters `β` and `γ` I defined a loss function

      def loss(point, data, recovered, s_0, i_0, r_0):
          size = len(data)
          beta, gamma = point
          def SIR(t, y):
              S = y[0]
              I = y[1]
              R = y[2]
              return [-beta*S*I/s_0, beta*S*I/s_0-gamma*I, gamma*I]
          solution = solve_ivp(SIR, [0, size], [s_0,i_0,r_0], t_eval=np.arange(0, size, 1), vectorized=True)
          l1 = np.sqrt(np.mean((solution.y[1] - data)**2))
          l2 = np.sqrt(np.mean((solution.y[2] - recovered)**2))
          alpha = 0.1
          return alpha * l1 + (1 - alpha) * l2
          
Next I used `solve_ivp` module from SciPy which helps to solve Initial value problem for a system of Ordinary Differential Equations (https://docs.scipy.org/doc/scipy/reference/generated/scipy.integrate.solve_ivp.html)
          
I have taken reference from this article here: https://www.lewuathe.com/covid-19-dynamics-with-sir-model.html , to define my loss function.

I have defined two stages in India one for the pre-lockdown period (2nd March - 25th March) and lockdown period from (25th Mar - 19th April). I have then estimated by `β` and `γ` based on the lockdown period and hence predicted for a period of next 6 months starting from April 20th. 

I set my `S_0`, i.e. the initial population for the SIR model to two values, one for the Best Case Scenario and Worst Case Scenario. For guesstimating the values of `S_0` intuitively I considered the below mentioned factors:

i) Tried to keep a bias of a higher population based on the data of most affected countries in the world (Europe & USA)
ii) The percentage of people who are actually being tested positive out of the total no. of tests done
iii) The mean age of the population of India is quite lesser compared to Europe and USA
iv) The climatic conditions and the immuntiy of Indians
v) Considering that Corona started off with people with travel history. There are many places in India which are underdeveloped, does not have an airport, and neither those people have priviledge to travel outside their place nor does anyone visit because of lack of development and industries
vi) Earlier declaration of lockdown compared to other countries

For better explanation please refer to the notebook provided

Data Sources:

For India: http://covid19india.org
For Rest of World: https://www.kaggle.com/duttadebadri/covid19-testing-rate-all-countries, https://www.kaggle.com/duttadebadri/covid19-india-complete-data, https://www.kaggle.com/sudalairajkumar/covid19-in-india, etc.

Tried to do the modeling for three different scenarios:

**i) Without Lockdown Scenario in India**:

![1](https://user-images.githubusercontent.com/24243687/80021971-f9726100-84f8-11ea-9f9c-17e479bf0f00.JPG)

**ii) Best Case Scenario with Lockdown for next 6 months from 20th April**:

![2](https://user-images.githubusercontent.com/24243687/80022054-20309780-84f9-11ea-8472-6f946497de0c.JPG)

**iii) Worst Case Scenario with Lockdown for next 6 months from 20th April**:

![3](https://user-images.githubusercontent.com/24243687/80022145-3fc7c000-84f9-11ea-9093-961d239a9ac2.JPG)

