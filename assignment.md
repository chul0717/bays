### Bayesian A/B Testing

**Include your code and answers in** `individual.py`.

Front-end web developers are interested in which design of their website yields more sales or some other metric of interest. They will route some fraction of visitors to site A, and the other fraction to site B, and record if the visit yielded a sale or not.

Forget everything you know about statistical testing for now. Let's start from scratch and answer our customer's most important question directly: what is the probability that the CTR for site A is larger than CTR for site B given the data from the experiment (i.e. a sequence of 0s and 1s in the case of click-through-rate)?

We'll call **pA** the true CTR for site A and **pB** the true CTR for site B. These are the values we are trying to figure out empirically.

For this exercise, we assume you've imported the following modules:

```python
import numpy as np
from scipy import stats
import matplotlib.pyplot as plt
```

1. You'll find some click-through data in the `data` folder. There are two files, one for each version of the site.
    * the 0 refers to a visit without a conversion
    * the 1 refers to a visit with a conversion.

    Read in the data into python and store it in two arrays, one for each version of the site.

2. We'll start by dealing with only the Site A data. We'd like to visualize our understanding of the CTR as the data streams in. Let's start with the uniform prior. So before we get any data, we will say that every probability of 0 to 1 is equally likely. We would like to plot our distribution.

    * To plot a continuous distribution, we need to make it discrete (since computers work that way). So we pick a large number of points to be our sample. In this case, take 100 points from 0 to 1 like this:

        ```python
        x = np.arange(0, 1.01, 0.01)
        ```

    * Then use the `pdf` function for your distribution (which `scipy.stats` nicely has for us). In this case, since we're looking at the uniform distribution, we do the following.

        ```python
        y = stats.uniform(0, 1).pdf(x)
        ```

    * Now we can use `ax.plot` to plot the data. You can use this function to plot the distribution with it shaded in:

        ```python
        def plot_with_fill(ax, x, y, label):
            lines = ax.plot(x, y, label=label, lw=2)
            ax.fill_between(x, 0, y, alpha=0.2, color=lines[0].get_c())
        ```

        Give it the label "Prior".

    * Do `plt.show()` to see the plot.

    * Did your "Prior" label show up? You probably also need to add a `ax.legend()` call (before `plt.show`).

3. We will be using a *beta* distribution to represent the distribution of *pA* (the CTR for site A). First, modify the above to use the beta distribution instead of the uniform distribution to verify to yourself that with parameters `alpha=1` and `beta=1`, the beta distribution *is* the uniform distribution.

4. Consider the data of siteA for the first 50 views. Draw a plot of the *posterior* after gathering this data. You should add the `alpha` parameter the number of conversions you've gotten. To the `beta` parameter, add the number of non-conversions you've gotten.

5. Overlay this new plot on top of the previous one. You should get something that looks like this:

    ![Prior and Posterior](images/prior_posterior.png)

6. After 50 views, we're starting to hone in on our prediction of *pA*. Overlay on the same graph the posterior after 50 views, 100 views, 200 views, 400 views and finally all 800 views.

    You should see as time progresses that we get more certain of the true value of *pA*.

7. Now we should understand how we're using the beta distribution to represent the distribution of the possible values of *pA*. Make a graph that has the final distributions for both *pA* and *pB* after all 800 views.

    You might want to use `ax.set_xlim` to change the limits of the x-axis so you can see the interesting part more clearly.

    You can see that the majority of the time site B is better than site A, but there is a chance that site A is better.

8. We now want to determine, given these distributions, what is the probability that site B is better than site A.

    We do this with a simulation. Draw 10,000 points from site A's beta distribution and 10,000 points from site B's distribution. Use either `stats.beta().rvs()` or `np.random.beta()` for this.

    You should get two arrays that look something like this:

    ```python
    array([ 0.08609807,  0.05759121,  0.0581405 , ...,  0.07907663,
            0.09122627,  0.0606501 ])
    ```

    This is simulating 10,000 times of drawing from our distributions. Count the number of times that what we drew from site B's distribution is larger than what we drew from site A's distribution. Divide by 10,000 to get the percent likelihood that site B is better than site A.

9. An X% [credible interval](https://en.wikipedia.org/wiki/Credible_interval) in a posterior distribution is analogous to a frequentist analysis's confidence intervals. One method of determining a credible interval is the highest density interval (HDI), which is the most dense interval of a posterior distribution containing X% of its mass. Another method is the equal-tailed interval, which is the interval containing X% of the posterior distribution's mass where the probability of being below the interval is as likely as being above it.

    Determine the 95% equal-tailed interval for site A's beta distribution using the simulations you just performed. (Hint: `scipy.stats` has a percentile function called `ppf`.)

10. A great thing about Bayesian A/B testing is that we can also answer the question, *What is the probability that site B is 2 percentage points better than site A*?

    Here, instead of needing `B > A`, we need `B > A + 0.02`.

    You should determine that we need more data to say this with confidence.

    Note, when you compare `B > A` you can calculate a single probability that this occurs in the simulation, but you can also do something richer, like calculate the
    entire distribution of `B - A`. Plot a histogram of this quantity. Try `B-A-.02`. What distribution do the shape of these histograms suggest? Why? What shape does it take on if you only use one observation of site_b's data? Is this a problem?

11. For a sanity check, see that you get similar results as you did in #8 using the frequentist approach. Which of the questions in #10 could you answer with a frequentist approach?

12. It's important to understand the business impact of your work as a data scientist. Imagine that your company is debating whether to switch to site B from site A. Assume the following:
    * the average click on site A yields $1.00 in profit
    * the average click on site B yields $1.05 in profit

    Should your company invest in switching to site B? Assume it is costly to do so, both in time and money. Explain. (**Hint**: The answer is not obvious.)
