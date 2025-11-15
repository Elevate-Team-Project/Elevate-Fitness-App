# System Operations and Calculations

This document provides a detailed explanation of the operations and calculations performed by the Elevate-Fitness-App.

## 1. `WeightGoalActivityAddComand` Operation

This is the foundational operation of the system. It is responsible for adding and updating a user's basic physical information, which includes:

- **Weight:** The user's body weight in kilograms.
- **Height:** The user's height in centimeters.
- **Age:** The user's age in years.
- **Gender:** The user's gender ("M" for male, "F" for female).

This information is essential as it forms the basis for all subsequent fitness calculations. The system stores this data and uses it to tailor fitness plans to the individual user.

## 2. `CalculateUserFitnessCommand` Operation and Calculations

This operation is the core of the fitness calculation engine. It takes the user's basic information and calculates key fitness metrics.

### a. Basal Metabolic Rate (BMR) Calculation

BMR is the number of calories your body needs to accomplish its most basic (basal) life-sustaining functions. The formula varies by gender:

- **For Males:** `BMR = 10 * weight (kg) + 6.25 * height (cm) - 5 * age (years) + 5`
- **For Females:** `BMR = 10 * weight (kg) + 6.25 * height (cm) - 5 * age (years) - 161`

### b. Total Daily Energy Expenditure (TDEE) Calculation

TDEE is an estimation of how many calories you burn per day when exercise is taken into account. It is calculated by multiplying the BMR by an activity factor.

`TDEE = BMR * Activity Factor`

The activity factors are based on the user's self-reported activity level:

| Activity Level | Factor |
| :--- | :--- |
| Rookie | 1.2 |
| Beginner | 1.375 |
| Intermediate | 1.55 |
| Advance | 1.725 |
| TrueBeast | 1.9 |

### c. Calorie Target Calculation

The daily calorie target is determined based on the user's fitness goal:

| Goal | Calculation |
| :--- | :--- |
| Lose Weight | `TDEE - 500` |
| Get Fitter | `TDEE` |
| Gain Weight | `TDEE + 300` |
| Gain More Flexible | `TDEE + 150` |
| Learn the Basic | `TDEE` |

### d. Status Determination

Finally, a status is assigned based on the calculated calorie target:

| Calorie Target | Status |
| :--- | :--- |
| <= 1800 | Weak |
| <= 2500 | Normal |
| > 2500 | Hard |

These calculated metrics (BMR, TDEE, Calorie Target, and Status) are then stored for the user and used to assign a fitness plan.

## 3. `AssignFitnessPlanCommand` Operation

This final operation brings everything together by assigning a concrete fitness plan to the user. It uses the results from the `CalculateUserFitnessCommand` to find a suitable plan.

The process is as follows:

1.  **Retrieve Latest User Stats:** The system fetches the most recent fitness statistics for the user, which include their goal, status, and calculated calorie target.

2.  **Find Matching Configuration:** It then searches for a `FitnessPlanConfig` that matches the user's stats based on the following criteria:
    *   The **Goal** in the configuration matches the user's goal.
    *   The **Status** in the configuration matches the user's status (Weak, Normal, Hard).
    *   The user's **Calorie Target** falls within the `MinCalorie` and `MaxCalorie` range defined in the configuration.

3.  **Assign Workout Plan:** If a matching configuration is found, the system assigns the corresponding `WorkoutPlan` to the user by returning the `PlanId`. If no match is found, an exception is thrown, indicating that no suitable plan could be located for the user's specific metrics and goals.
