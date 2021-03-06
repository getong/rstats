# Description

The Erlang ```rstats``` module implements random sampling of:

* Poisson distributions (http://en.wikipedia.org/wiki/Poisson_distribution)
* Exponential distributions (http://en.wikipedia.org/wiki/Exponential_distribution),
* Normal distributions (https://en.wikipedia.org/wiki/Normal_distribution)
* Truncated normal distributions (https://en.wikipedia.org/wiki/Truncated_normal_distribution)
* Discrete probability distributions (https://en.wikipedia.org/wiki/Alias_method).

The algorithms for sampling the poisson, exponential and normal distributions are re-implemented from R's native C implementions which
themselves are based on the following papers:

Ahrens, J.H. and Dieter, U. (1982). Computer generation of Poisson deviates from modified normal distributions.
ACM Trans. Math. Software 8, 163-179.

and

Ahrens, J.H. and Dieter, U. (1972). Computer methods for sampling from the exponential and normal distributions.
Comm. ACM, 15, 873-882.

The algorithm for sampling discrete probability distributions is based on the this paper:

Walker, A. J. (1977). "An Efficient Method for Generating Discrete Random Variables with General Distributions".
ACM Transactions on Mathematical Software 3 (3): 253

and was implemented in Erlang by Sergey Prokhorov (See https://gist.github.com/seriyps/5593193 for original implementation)

The sampling of the truncated normal distribution is a re-implementation of the CRAN package truncnormal (https://cran.r-project.org/web/packages/truncnorm/index.html)
The C code is included in the reference folder.

Besides that there are a few helper functions for generating CSV files to import them into R for
comparison and validation as well as common copies of floor, ceiling, fsign and fact.


### Usage

```erlang
rstats:rpois(Lamda)                            % returns random sample from Poisson distribution
rstats:rexp()                                  % returns random sample from Exponential Distribution
rstats:rexp(Scale)                             % returns scaled random sample from Exponential Distribution
rstats:rnormal(Mean, Sigma)                    % returns random sample from Normal Distribution
rstats:rtruncnormal(Min, Max, Mean, Sd)        % returns a sample from truncated normal distribution
                                               % instead of Min/Max you can use the atom infinity
                                               % if both are infinity, the regular normal distribution is sampled
rstats:rtruncnormal(N, Min, Max, Mean, Sd)     % returns n samples from truncated normal distribution
rstats:dnorm(X, Mean, Sd)                      % Compute density of the normal distribution
rstats:random_uniform(Min, Max)                % Returns a sample (float) of uniform distribution within range (fast)
rstats:random_uniform_integer(Min, Max)        % Returns a sample (integer) of uniform distribution within range (fast)
rstats:random_uniform(N, Min, Max)             % Returns n samples (float) of uniform distribution within range (fast)
rstats:random_uniform_integer(N, Min, Max)     % Returns n samples (integer) of uniform distribution within range (fast)
rstats:walker_lookup_table([{Key, Weight}, …]) % Create Walker Choice Lookup Table
rstats:walker_choice(LookupTable)              % Draw random sample from weighted distribution
```

### Create Large Samples

```erlang
rstats:write_csv("/tmp/samples.csv", [rstats:rpois(32.0) || _ <- lists:seq(1,1000000)]).

rstats:write_float_csv("/tmp/samples.csv", [rstats:rexp()) || _ <- lists:seq(1,1000000)]).

rstats:write_float_csv("/tmp/samples.csv", rstats:rtruncnormal(1000000, 1, 23, 7, 3)).
```

#### Analyze Data in R with:

```R
# Native Implementation
rpois(1000000, 20.0)
rexp(1000000)

# Truncated Normal Distribution in R
install.packages('truncnorm')
library(truncnorm)
rtruncnorm(100, a=-1, b=5, mean=3, sd=2)

# Read CSV
results <- read.table("/tmp/samples.csv")

# Analyze Samples
mean(results[1:1000000, 1])
sd(results[1:1000000, 1])
plot(hist(results[1:1000000, 1]))

# Compare with native implementation
plot(hist(rpois(1000000, 20.0)))
plot(hist(rexp(1000000)))

# Look at numbers / occurrences instead of Histograms
table(unlist(results[1:1000000, 1]))

# Plot Bar Graphs
barplot(table(results[1:1000000, 1]))

```

# NOTE

I've included the original native C implementation of rpois in R which is licensed under the GNU General Public License

# Further Optimizations

* Exchange lists:seq with recursion

# Contributors

* Walker Alias Method originally implemented by Sergey Prokhorov

# License

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
