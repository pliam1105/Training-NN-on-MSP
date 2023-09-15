# Training Neural Networks on the data from: “[On the parameter combinations…](https://academic.oup.com/pnasnexus/article/1/4/pgac154/6697026)”
I examined the reduced-MSP model from the above paper and gathered data from Runge-Kutta integrations of the equations, while varying the parameters that are incorporated into the equations of the systems. Then I trained two types of Neural Networks based on these data and evaluated their accuracy.
</br>
## Experiment: Varying the parameters of the system
* First, I imported the necessary libraries, determined the parameters of the system based on the ones the paper uses: ($ k_{f, 1} = 0.71, k_{f, 2} = 0.97, k_{r, 1} = 19, k_{r, 2} = 7000, k_{cat, 1} = 6700, k_{cat, 2} = 10000 $) made the function for the derivatives of the system, the functions for the *Runge-Kutta* and *Euler* integration maps and the one that computes the trajectory using these, including the parameters of the system as an input to the functions.
* Next, I took a sample of $2000$ random sets of values for the parameters, with deviation of $-75\%$ to $-100\%$ from the ones given by the paper (this is where the steady state starts to change), and stored the trajectories (from Runge-Kutta) using these starting values. From these trajectories, I then made the training and test data, taking the points of $80\%$ of the trajectories as training and $20\%$ as testing. $20\%$ of the testing points are used as validation dataset for the training.
* Then, I trained a Neural Network to learn the time-1 map of the system, and consists of the input and output layers (input is a point in the $S0, S1, S2$ 3D space and the parameters of the system and output is the point after time $dt$) and $2$ hidden fully connected layers with $15$ nodes each (after normalizing the input values to the same magnitude). An overview of the model is shown below:<br><br>
<img src="On the parameters paper/nn_map_diagram.png" width="50%"><br>
* I also trained a model that predicts the derivatives of the values at a certain point in the $S0, S1, S2$ space (the RHS of the differential equation of the system). Its structure is the same as the previous model, but the output contains the derivatives instead of the values of the map. The training data are also based on the trajectories as before. An overview of the model:<br><br>
<img src="On the parameters paper/nn_dif_diagram.png" width="50%"><br>
* Then I plotted the trajectories of the two different models, along with the ones with the Runge-Kutta method, with 6 sets of randomly selected parameters of the test data, in order to evaluate their accuracy. Dotted line is the *derivative* Neural Network trajectory, solid line the Runge-Kutta, and dashed line the *time-1 map* Neural Network. Also, green line is $S0$, blue line $S1$, and red line $S2$.<br>
<table>
    <tr>
        <td><img src="On the parameters paper/PLOTS DIFFERENT PARAMS 3/trajectories1.png"></td>
        <td><img src="On the parameters paper/PLOTS DIFFERENT PARAMS 3/trajectories2.png"></td>
        <td><img src="On the parameters paper/PLOTS DIFFERENT PARAMS 3/trajectories3.png"></td>
    </tr>
    <tr>
        <td><img src="On the parameters paper/PLOTS DIFFERENT PARAMS 3/trajectories4.png"></td>
        <td><img src="On the parameters paper/PLOTS DIFFERENT PARAMS 3/trajectories5.png"></td>
        <td><img src="On the parameters paper/PLOTS DIFFERENT PARAMS 3/trajectories6.png"></td>
    </tr>
</table>

* In all the trajectories, I stopped time-integrating when the system reached a steady state, which was detected when the norm of the difference of the new state with the previous state was less than $0.2\%$ of the norm of the initial state. Then I calculated the norm of the difference of the steady states computed with the time-1 map and the derivative Neural Network, with the steady state computed with the Runge-Kutta method. The results are shown below (for the trajectories plotted above):
<table align="center">
    <tr>
        <td></td>
        <th colspan=6># of set of randomly selected test parameters</th>
    </tr>
    <tr>
        <td></td>
        <td>1st</td>
        <td>2nd</td>
        <td>3rd</td>
        <td>4th</td>
        <td>5th</td>
        <td>6th</td>
    </tr>
    <tr>
        <th>Differential NN Error in Steady State</th>
        <td>0.017</td>
        <td>2.35</td>
        <td>0.03</td>
        <td>0.037</td>
        <td>0.08</td>
        <td>0.063</td>
    </tr>
    <tr>
        <th>Time-1 Map NN Error in Steady State</th>
        <td>2.83</td>
        <td>0.97</td>
        <td>0.34</td>
        <td>0.17</td>
        <td>0.58</td>
        <td>0.5</td>
    </tr>
</table>