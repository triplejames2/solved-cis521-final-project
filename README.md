Download Link: https://assignmentchef.com/product/solved-cis521-final-project
<br>
In this assignment you will use the forward and backward algorithms to compute the probability of an observation sequence given a Hidden Markov Model and then to re-estimate the parameters of the HMM to maximize the probability. The terminology used throughout this assignment follows that of the lecture slides on HMMs except that it uses <em>t </em>= {0<em>,…,T </em>− 1} instead of <em>t </em>= {1<em>,…,T</em>}.

A skeleton file homework11.py containing empty definitions for each question has been provided. Since portions of this assignment will be graded automatically, none of the names or function signatures in this file should be modified. However, you are free to introduce additional variables or functions if needed.

You may import definitions from any standard Python library and are encouraged to do so in case you find yourself reinventing the wheel.

You will find that in addition to a problem specification, most programming questions also include a pair of examples from the Python interpreter. These are meant to illustrate typical use cases, and should not be taken as comprehensive test suites.

You are strongly encouraged to follow the Python style guidelines set forth in PEP 8, which was written in part by the creator of Python. However, your code will not be graded for style.

Once you have completed the assignment, you should submit your file on Eniac using the following turnin command, where the flags -c and -p stand for ”course” and ”project”, respectively.

turnin -c cis521 -p hw11 homework11.py

You may submit as many times as you would like before the deadline, but only the last submission will be saved. To view a detailed listing of the contents of your most recent submission, you can use the following command, where the flag -v stands for ”verbose”.

turnin -c cis521 -p hw11 -v

<h1>1        Hidden Markov Model [95 points]</h1>

<h2>1.1     [5 points]</h2>

Write a function load corpus(path) to load the corpus and return it as a single string. All letters should be converted to lowercase and any characters other than letters or a single space should be removed.

&gt;&gt;&gt; s = load_corpus(“Brown_sample.txt”)

&gt;&gt;&gt; len(s)

2824

&gt;&gt;&gt; s[1208:1228]

“to the best interest”

<h2>1.2     [5 points]</h2>

Write a function load probabilities(path) that loads the probability vector at the given path and returns it. The probability vector is a tuple of the form (initial probabilties, transition probabilities, emission probabilities). These probabilities are already in log-space.

<strong>Initial probabilties: </strong>dictionary of the form {state<em><sub>i</sub></em>: probability that state<em><sub>i </sub></em>is a start state}

<strong>Transition probabilties: </strong>dictionary of the form {state<em><sub>i</sub></em>: {state<em><sub>j</sub></em>: probability of transitioning from state<em><sub>i </sub></em>to state<em><sub>j</sub></em>}}

<strong>Emission probabilities: </strong>dictionary of the form {state<em><sub>i</sub></em>: {symbol<em><sub>k</sub></em>: probability of being in state<em><sub>i </sub></em>and emitting symbol<em><sub>k</sub></em>}}

<table width="633">

 <tbody>

  <tr>

   <td width="314">&gt;&gt;&gt; p = load_probabilities(…                                            “simple.pickle”)&gt;&gt;&gt; p[1][1]{1: -0.6931471805599453, 2: -0.6931471805599453}</td>

   <td width="5"></td>

   <td width="314">&gt;&gt;&gt; p = load_probabilities(…                                              “prob_vector.pickle”)&gt;&gt;&gt; p[1][1]{1: -0.9394332049935226,2: -1.663989250166688,3: -2.563281364593492, 4: -1.070849586518945}</td>

  </tr>

 </tbody>

</table>

Then, in the HMM class, write an initialization method init (self, probabilities) that uses the probability vector to initialize the interal variables of the hidden Markov model.

<h2>1.3     [10 points]</h2>

Write a method forward(self, sequence) which takes a sequence and returns a list of dictionaries. Each dictionary is of the form {<em>state<sub>i </sub></em>: <em>α<sub>t</sub></em>(<em>i</em>)}, 1 ≤ <em>i </em>≤ <em>N, </em>0 ≤ <em>t </em>≤ <em>T </em>− 1, where <em>N </em>is the number of states and <em>T </em>is the length of the input sequence. Recall from the lecture slides that <em>α<sub>t</sub></em>(<em>i</em>) is the probability that the partial sequence up to time <em>t </em>has been seen and the current state is <em>i</em>.

The computations here, and throughout the assignment, should be done using log-space to avoid underflow. Multiplication becomes addition in log space and division becomes subtraction, but there is not a log-space equivalent of summation, so values must be summed in normal space. However, the values being computed will become so small that they become 0 in normal space. The solution to this problem is something called the log-sum-exp trick which uses the following identity:

Setting a to be the max over all <em>x<sub>n </sub></em>will increase the values so that they can be summed in normal-space.

<table width="633">

 <tbody>

  <tr>

   <td width="314">&gt;&gt;&gt; s = “the cat ate the rat”&gt;&gt;&gt; p = load_probabilities(…                                            “simple.pickle”)&gt;&gt;&gt; h = HMM(p)&gt;&gt;&gt; f = h.forward(s)&gt;&gt;&gt; f[10]{1: -22.0981588201684, 2: -22.0981588201684}</td>

   <td width="5"></td>

   <td width="314">&gt;&gt;&gt; s = load_corpus(“Brown_sample.txt”)&gt;&gt;&gt; p = load_probabilities(…                                              “prob_vector.pickle”)&gt;&gt;&gt; h = HMM(p)&gt;&gt;&gt; f = h.forward(s)&gt;&gt;&gt; f[1400]{1: -4570.10024680558,2: -4569.896509256886,3: -4569.956231590213, 4: -4569.542222483007}</td>

  </tr>

 </tbody>

</table>

<h2>1.4     [10 points]</h2>

Write a method forward probability(self, alpha) that returns the forward probability of this HMM for that sequence.

<table width="633">

 <tbody>

  <tr>

   <td width="314">&gt;&gt;&gt; s = “the cat ate the rat”&gt;&gt;&gt; p = load_probabilities(…                                            “simple.pickle”)&gt;&gt;&gt; h = HMM(p)&gt;&gt;&gt; h.forward_probability(h.forward(s))-36.972292832050975</td>

   <td width="5"></td>

   <td width="314">&gt;&gt;&gt; s = load_corpus(“Brown_sample.txt”)&gt;&gt;&gt; p = load_probabilities(…                                              “prob_vector.pickle”)&gt;&gt;&gt; h = HMM(p)&gt;&gt;&gt; h.forward_probability(h.forward(s))-9201.34957430782</td>

  </tr>

 </tbody>

</table>

<h2>1.5     [10 points]</h2>

Write a method backward(self, sequence) which takes a sequence and returns a list of dictionaries. Each dictionary is of the form {<em>state<sub>i </sub></em>: <em>β<sub>t</sub></em>(<em>i</em>)}, 1 ≤ <em>i </em>≤ <em>N, </em>0 ≤ <em>t </em>≤ <em>T </em>− 1, where <em>N </em>is the number of states and <em>T </em>is the length of the input sequence. Recall from the lecture slides that <em>β<sub>t</sub></em>(<em>i</em>) is probability that the partial sequence from <em>t </em>+ 1 to <em>T </em>− 1 will be seen given that the current state is <em>i</em>.

<table width="633">

 <tbody>

  <tr>

   <td width="314">&gt;&gt;&gt; s = “the cat ate the rat”&gt;&gt;&gt; p = load_probabilities(…                                            “simple.pickle”)&gt;&gt;&gt; h = HMM(p)&gt;&gt;&gt; b = h.backward(s)&gt;&gt;&gt; b[9]{1: -17.513191341497826, 2: -17.513191341497826}</td>

   <td width="5"></td>

   <td width="314">&gt;&gt;&gt; s = load_corpus(“Brown_sample.txt”)&gt;&gt;&gt; p = load_probabilities(…                                              “prob_vector.pickle”)&gt;&gt;&gt; h = HMM(p)&gt;&gt;&gt; b = h.backward(s)&gt;&gt;&gt; b[1424]{1: -4553.117090965298, 2: -4553.249309905892,3: -4553.085375790753, 4: -4553.140279571696}</td>

  </tr>

 </tbody>

</table>

<h2>1.6     [10 points]</h2>

Write a method backward probability(self, beta, sequence) that returns the backward probability of this HMM for that sequence. This should match the forward probability, but may not be an exact match due to floating point inaccuracies.

<table width="633">

 <tbody>

  <tr>

   <td width="314">&gt;&gt;&gt; s = “the cat ate the rat”&gt;&gt;&gt; p = load_probabilities(…                                            “simple.pickle”)&gt;&gt;&gt; h = HMM(p)&gt;&gt;&gt; h.backward_probability(…                                            h.backward(s),s)-36.97229283205097</td>

   <td width="5"></td>

   <td width="314">&gt;&gt;&gt; s = load_corpus(“Brown_sample.txt”)&gt;&gt;&gt; p = load_probabilities(…                                              “prob_vector.pickle”)&gt;&gt;&gt; h = HMM(p)&gt;&gt;&gt; h.backward_probability(…                                            h.backward(s),s)-9201.349574307758</td>

  </tr>

 </tbody>

</table>

<h2>1.7     [10 points]</h2>

Write a method xi matrix(self, t, sequence, alpha, beta) which returns a dictionary of the form{state<em><sub>i</sub></em>: {state<em><sub>j</sub></em>:<em>ξ<sub>t</sub></em>(<em>i,j</em>)}}, where <em>ξ<sub>t</sub></em>(<em>i,j</em>) is the probability of being in state <em>i </em>at time <em>t </em>and going to state <em>j </em>given the current parameters of the HMM.

<table width="633">

 <tbody>

  <tr>

   <td width="314">&gt;&gt;&gt; s = “the cat ate the rat”&gt;&gt;&gt; p = load_probabilities(…                                            “simple.pickle”)&gt;&gt;&gt; h = HMM(p)&gt;&gt;&gt; h.xi_matrix(5,s,h.forward(s),…                                            h.backward(s))[2]{1: -1.3862943611198943, 2: -1.3862943611198943}</td>

   <td width="5"></td>

   <td width="314">&gt;&gt;&gt; s = load_corpus(“Brown_sample.txt”)&gt;&gt;&gt; p = load_probabilities(…                                              “prob_vector.pickle”)&gt;&gt;&gt; h = HMM(p)&gt;&gt;&gt; h.xi_matrix(500,s,h.forward(s),…                                            h.backward(s))[1]{1: -2.5704875729134073, 2: -3.418873166145204,3: -3.8974061320204783,4: -2.080487933135373}</td>

  </tr>

 </tbody>

</table>

<h2>1.8     [25 points]</h2>

Write a method forward backward(self, sequence) that re-estimates the parameters of the HMM and returns them in the same form as load probabilities(path), that is, a tuple of the form (initial probabilties, transition probabilities, emission probabilities). The formulas for re-estimation can be found in the lecture slides on HMMs, but they will need to be adjusted to account for changed range of <em>t</em>.

Re-estimation of initial probabilities:

<em>π</em>ˆ<em><sub>i </sub></em>= <em>expected number of times in state s<sub>i </sub>at time </em>0

Re-estimation of transition probabilities:

Re-estimation of emission probabilities:

The re-restimations will require computing <em>γ<sub>t</sub></em>(<em>i</em>) which is the probability of being in state <em>s<sub>i </sub></em>at time <em>t </em>and can be computed in two ways:

One important thing to note is that re-estimation of the transition probabilities ˆ<em>a<sub>i,j </sub></em>is done over the range <em>t </em>= {0<em>,…,T </em>− 2} while re-estimation of the emission probabilities <sup>ˆ</sup><em>b<sub>i</sub></em>(<em>k</em>) is done over the range <em>t </em>= {0<em>,…,T </em>− 1}, where T is the length of the input sequence. Both of these re-estimations use <em>γ<sub>t</sub></em>(<em>i</em>), but when <em>t </em>= <em>T </em>−1, <em>γ<sub>t</sub></em>(<em>i</em>) cannot be computed using the <em>ξ </em>values and must be computed using the other method.

<table width="633">

 <tbody>

  <tr>

   <td width="314">&gt;&gt;&gt; s = “the cat ate the rat”&gt;&gt;&gt; p = load_probabilities(…                                            “simple.pickle”)&gt;&gt;&gt; h = HMM(p)&gt;&gt;&gt; p2 = h.forward_backward(s)&gt;&gt;&gt; h2 = HMM(p2)&gt;&gt;&gt; h2.forward_probability(…                                           h2.forward(s))-34.37400550438377</td>

   <td width="5"></td>

   <td width="314">&gt;&gt;&gt; s = load_corpus(“Brown_sample.txt”)&gt;&gt;&gt; p = load_probabilities(…                                              “prob_vector.pickle”)&gt;&gt;&gt; h = HMM(p)&gt;&gt;&gt; p2 = h.forward_backward(s)&gt;&gt;&gt; h2 = HMM(p2)&gt;&gt;&gt; h2.forward_probability(…                                           h2.forward(s))-8070.961574771892</td>

  </tr>

 </tbody>

</table>

<h2>1.9     [10 points]</h2>

Write a method update(self,sequence,cutoff threshold) that repeatedly runs forward backward(sequence) to iteratively re-estimate the HMM probabilities. The re-estimation should stop once the increase in probability falls below the cutoff threshold, otherwise it will run much longer as it makes incremental improvements to the probability.

&gt;&gt;&gt; s = load_corpus(“Brown_sample.txt”)

&gt;&gt;&gt; p = load_probabilities(“prob_vector.pickle”)

&gt;&gt;&gt; h = HMM(p)

&gt;&gt;&gt; h.update(s, 1)

&gt;&gt;&gt; h.forward_probability(h.forward(s))

-7384.200943071948

<h1>2       Feedback [5 points]</h1>

<strong>2.1     [1 points]</strong>

Approximately how long did you spend on this assignment?

<h2>2.2     [2 points]</h2>

Which aspects of this assignment did you find most challenging?       Were there any significant stumbling blocks?

<h2>2.3     [2 points]</h2>

Which aspects of this assignment did you like? Is there anything you would have changed?