[The Compass chart - Amplitude](https://amplitude.com/docs/analytics/charts/compass/compass-aha-moment)

A key step in driving growth is discovering your users' "a-ha" moments. An "a-ha" moment happens when a **new user** makes the decision, consciously or unconsciously, to become an **active user** of your product.

The most famous example comes from Facebook. Facebook discovered that users who added at least seven friends in the first ten days almost always stuck around. Users who didn't almost always churned. This insight helped Facebook drive retention by encouraging the right user behavior: adding friends.

The Compass chart helps you achieve the same outcome. A Compass analysis scans your user data and identifies these behaviors, giving you the insights you need to improve your product and drive sustainable growth.

If you have a reasonable proportion above the threshold, view the correlation between reaching the event frequency and retention. Look at the **positive predictive value** **(PPV)** and **sensitivity**.

PPV looks at the ratio of users who reached the event frequency and retained (**true positive**) to all users who reached the event frequency (**true positive + false positive**). Sensitivity looks at the ratio of users that retained and reached the event frequency (true positive) to all users retained (true positive + false negative). You want both to be high.

Imagine PPV is high but sensitivity is low. The event **predicts** retention, but few new users reach the threshold. The event is a promising candidate for experimentation to see if you can encourage more users to trigger it. The result also means another inflection metric might exist that you haven't looked at yet, since people who aren't reaching this frequency are still retained.

The event frequency captures many of the retained users, but the total retention for the product is likely low. This **isn't a good candidate** for an inflection metric, because either the product's retention is low or a high percentage of users meeting the event frequency aren't retained.

The inflection moment should be a positive predictor. You also want to ensure that when a user **fails** to reach the threshold, the failure is a **negative** predictor of retention, in other words, churn. Amplitude captures this through the **negative predictive value** **(NPV)** and **specificity**.

The Compass analysis tries to uncover event frequencies that maximize the upper-left (true positive) and bottom-right (true negative) quadrants of the contingency matrix. If you're familiar with statistics, this minimizes the [Type I and Type II errors](https://en.wikipedia.org/wiki/Type_I_and_type_II_errors).

These inflection metrics balance all five of the detailed statistics in the contingency matrix. Depending on the type of product, a good correlation falls in the range of 0.2 to 0.4, depending on the number of performance days (1 to 7) for the event.
