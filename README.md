# coo


std::pair<double, double> Antithetic_with_std_err(double S0, double X, double r, double sigma, double T, int n, double alpha, double beta, double theta, double lambda)


{


    // Initialize random number generator and normal distribution


    static std::mt19937 rng;


    std::normal_distribution<> N(0., 1.);



    // Compute constants


    double f = S0 * (2. - std::exp(alpha * T) - std::exp(beta * T)) + theta * (3. - std::exp(alpha * T) - std::exp(beta * T));


    double vsquare = sigma * std::exp((2. * alpha - beta) * T) * (1./4.) * std::pow(3. * S0 + theta, lambda);



    // Initialize sum, sum of squares, and antithetic sum


    double sum = 0.0;


    double sum_sq = 0.0;


    double antithetic_sum = 0.0;



    // Compute option price and standard error using antithetic variables method


    for (int i = 0; i < n/2; ++i)


    {


        // Generate two independent standard normal variates using N(rng) and -N(rng)


        double phi = N(rng);


        double phi_antithetic = -phi;



        // Compute stock price at expiration using antithetic variables method


        double ST = f + vsquare * std::sqrt(T) * phi;


        double ST_antithetic = f + vsquare * std::sqrt(T) * phi_antithetic;



        // Compute payoff at expiration using antithetic variables method


        double payoff = std::max(X - ST, 0.0);


        double payoff_antithetic = std::max(X - ST_antithetic, 0.0);



        // Accumulate the sum and antithetic sum of payoffs


        sum += payoff;


        antithetic_sum += payoff_antithetic;



        // Accumulate the sum of squares of payoffs


        sum_sq += payoff * payoff + payoff_antithetic * payoff_antithetic;


    }



    // Compute mean payoff, antithetic mean payoff, and option price using antithetic variables method


    double mean_payoff = (sum + antithetic_sum) / n;


    double option_price = std::exp(-r * T) * mean_payoff;



    // Compute variance and standard error using antithetic variables method


    double var = (sum_sq - 2 * sum * antithetic_sum / (n) + antithetic_sum * antithetic_sum / n) / (n - 1);


    double std_err = std::sqrt(var /(n));



    // Return option price and standard error as a pair


    return std::make_pair(option_price, std_err);


}
