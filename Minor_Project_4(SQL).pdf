USE bingeplay;

-- =====================================================
-- Q1 - ACTIVE REVENUE
-- =====================================================
SELECT
    COUNT(*) AS active_subscriptions,
    SUM(monthly_price_inr) AS monthly_recurring_revenue
FROM subscriptions
WHERE status = 'active'
  AND (
        end_date IS NULL
        OR end_date > '2024-06-30'
      );
      
-- =====================================================
-- Q2 - SIGNUP MOMENTUM
-- =====================================================
SELECT
    MONTH(signup_date) AS month_number,
    MONTHNAME(signup_date) AS month,
    COUNT(*) AS signup_count
FROM users
WHERE signup_date >= '2024-01-01'
  AND signup_date < '2024-07-01'
GROUP BY
    MONTH(signup_date),
    MONTHNAME(signup_date)
ORDER BY month_number;


-- ============================================================
-- Q3 - DEVICE ANALYTICS
-- ============================================================

-- CHECK WATCH_SESSIONS COLUMNS
DESCRIBE watch_sessions;

-- ============================================================
-- Q3 - DEVICE ANALYTICS
-- ============================================================

SELECT
    device_type,
    COUNT(*) AS total_sessions,
    ROUND(SUM(watch_minutes), 2) AS total_watch_minutes,
    ROUND(AVG(watch_minutes), 2) AS average_watch_minutes,
    ROUND(AVG(completed) * 100, 2) AS completion_rate
FROM watch_sessions
GROUP BY device_type
ORDER BY total_sessions DESC;


-- ============================================================
-- Q4 - SHOW PERFORMANCE
-- ============================================================

-- CHECK SHOWS COLUMNS
DESCRIBE shows;

SELECT
    s.show_id,
    s.title,
    s.category,
    COUNT(w.session_id) AS total_sessions,
    ROUND(SUM(w.watch_minutes), 2) AS total_watch_minutes,
    ROUND(AVG(w.watch_minutes), 2) AS average_watch_minutes,
    ROUND(AVG(w.completed) * 100, 2) AS completion_rate
FROM shows s
JOIN watch_sessions w
    ON s.show_id = w.show_id
GROUP BY
    s.show_id,
    s.title,
    s.category
ORDER BY total_watch_minutes DESC
LIMIT 10;


-- ============================================================
-- Q5 - CATEGORY PERFORMANCE
-- ============================================================

SELECT
    s.category,
    COUNT(w.session_id) AS total_sessions,
    COUNT(DISTINCT w.user_id) AS unique_users,
    ROUND(SUM(w.watch_minutes), 2) AS total_watch_minutes,
    ROUND(AVG(w.watch_minutes), 2) AS average_watch_minutes,
    ROUND(AVG(w.completed) * 100, 2) AS completion_rate
FROM shows s
JOIN watch_sessions w
    ON s.show_id = w.show_id
GROUP BY
    s.category
ORDER BY total_watch_minutes DESC;


-- ============================================================
-- Q6 - LANGUAGE PERFORMANCE
-- ============================================================

SELECT
    s.language,
    COUNT(w.session_id) AS total_sessions,
    COUNT(DISTINCT w.user_id) AS unique_users,
    ROUND(SUM(w.watch_minutes), 2) AS total_watch_minutes,
    ROUND(AVG(w.watch_minutes), 2) AS average_watch_minutes,
    ROUND(AVG(w.completed) * 100, 2) AS completion_rate
FROM shows s
JOIN watch_sessions w
    ON s.show_id = w.show_id
GROUP BY
    s.language
ORDER BY total_watch_minutes DESC;


-- ============================================================
-- Q7 - SUBSCRIPTION PLAN ANALYSIS
-- ============================================================

SELECT
    s.plan,
    COUNT(*) AS total_subscriptions,
    COUNT(DISTINCT s.user_id) AS unique_users,
    ROUND(AVG(s.monthly_price_inr), 2) AS average_monthly_price,
    ROUND(SUM(s.monthly_price_inr), 2) AS monthly_revenue
FROM subscriptions s
GROUP BY
    s.plan
ORDER BY monthly_revenue DESC;



-- ============================================================
-- Q8 - USER ENGAGEMENT ANALYSIS
-- ============================================================

SELECT
    user_id,
    COUNT(*) AS total_sessions,
    ROUND(SUM(watch_minutes), 2) AS total_watch_minutes,
    ROUND(AVG(watch_minutes), 2) AS average_watch_minutes
FROM watch_sessions
WHERE user_id IS NOT NULL
GROUP BY user_id
HAVING COUNT(*) >= 20
ORDER BY total_watch_minutes DESC
LIMIT 20;

-- ============================================================
-- Q9 - RATING ANALYSIS
-- ============================================================

SELECT
    s.show_id,
    s.title,
    s.category,
    COUNT(r.rating_id) AS total_ratings,
    ROUND(AVG(r.stars), 2) AS average_rating,
    MIN(r.stars) AS minimum_rating,
    MAX(r.stars) AS maximum_rating
FROM shows s
JOIN ratings r
    ON s.show_id = r.show_id
GROUP BY
    s.show_id,
    s.title,
    s.category
HAVING COUNT(r.rating_id) >= 30
ORDER BY average_rating DESC
LIMIT 20;

-- ============================================================
-- Q10 - USER RATING ACTIVITY
-- ============================================================

SELECT
    user_id,
    COUNT(*) AS total_ratings,
    ROUND(AVG(stars), 2) AS average_rating_given,
    MIN(stars) AS minimum_rating_given,
    MAX(stars) AS maximum_rating_given
FROM ratings
GROUP BY user_id
HAVING COUNT(*) >= 5
ORDER BY total_ratings DESC
LIMIT 20;


-- ============================================================
-- Q11 - RATING DISTRIBUTION
-- ============================================================

SELECT
    stars,
    COUNT(*) AS rating_count,
    ROUND(
        COUNT(*) * 100.0 / (SELECT COUNT(*) FROM ratings),
        2
    ) AS percentage
FROM ratings
GROUP BY stars
ORDER BY stars DESC;


-- ============================================================
-- Q12 - USER ENGAGEMENT ANALYSIS
-- ============================================================

SELECT
    user_id,
    COUNT(*) AS total_sessions,
    ROUND(SUM(watch_minutes), 2) AS total_watch_minutes,
    ROUND(AVG(watch_minutes), 2) AS average_watch_minutes
FROM watch_sessions
WHERE user_id IS NOT NULL
GROUP BY user_id
HAVING COUNT(*) >= 20
ORDER BY total_watch_minutes DESC
LIMIT 20;


-- ============================================================
-- Q5 - ORIGINALS VS ACQUIRED
-- ============================================================

SELECT
    CASE
        WHEN is_original = 1 THEN 'Original'
        ELSE 'Acquired'
    END AS content_type,
    COUNT(*) AS number_of_shows,
    ROUND(AVG(imdb_rating), 2) AS average_imdb_rating,
    ROUND(AVG(release_year), 2) AS average_release_year
FROM shows
GROUP BY is_original
ORDER BY average_imdb_rating DESC;

-- ============================================================
-- Q6 - BINGE DAY DETECTION
-- ============================================================

WITH binge_days AS (
    SELECT
        user_id,
        show_id,
        session_date,
        COUNT(*) AS session_count
    FROM watch_sessions
    WHERE session_date >= '2024-04-01'
      AND session_date <= '2024-06-30'
      AND user_id IS NOT NULL
    GROUP BY
        user_id,
        show_id,
        session_date
    HAVING COUNT(*) >= 5
)

SELECT
    COUNT(*) AS total_binge_days,
    (
        SELECT user_id
        FROM binge_days
        GROUP BY user_id
        ORDER BY COUNT(*) DESC
        LIMIT 1
    ) AS user_with_most_binge_days,
    (
        SELECT COUNT(*)
        FROM binge_days bd
        WHERE bd.user_id = (
            SELECT user_id
            FROM binge_days
            GROUP BY user_id
            ORDER BY COUNT(*) DESC
            LIMIT 1
        )
    ) AS most_binge_days
FROM binge_days;
-- Findings:
-- 414 binge days were identified in Q2 2024.
-- User U00420 had the highest number of binge days, with 2 binge days.

-- Q7 — Q1 Signups Who Never Watched--
SELECT
    COUNT(*) AS q1_signups,
    SUM(
        CASE
            WHEN NOT EXISTS (
                SELECT 1
                FROM watch_sessions ws
                WHERE ws.user_id = u.user_id
            )
            THEN 1
            ELSE 0
        END
    ) AS never_watched
FROM users u
WHERE MONTH(u.signup_date) BETWEEN 1 AND 3
  AND YEAR(u.signup_date) = 2024;
  -- Findings:
-- There were 1,250 users who signed up in Q1 2024.
-- 226 of these users had never watched any content.


-- Q8 — The over-paying Premium/Family users --
SELECT u.user_id
FROM users u
JOIN subscriptions s
    ON u.user_id = s.user_id
WHERE s.plan IN ('Premium', 'Family')
  AND s.status = 'active'
  AND s.start_date = (
      SELECT MAX(s2.start_date)
      FROM subscriptions s2
      WHERE s2.user_id = s.user_id
        AND s2.status = 'active'
        AND s2.start_date <= '2024-06-30'
  )
  AND EXISTS (
      SELECT 1
      FROM watch_sessions w
      WHERE w.user_id = u.user_id
  )
  AND NOT EXISTS (
      SELECT 1
      FROM watch_sessions w
      JOIN shows sh
          ON w.show_id = sh.show_id
      WHERE w.user_id = u.user_id
        AND sh.min_plan IN ('Premium', 'Family')
  );
  
  
  -- Q9 – Upgrade Success Cohort. --
  
  -- Q9 - UPGRADE SUCCESS COHORT

SELECT
    u.user_id,
    u.signup_date,
    MIN(s.start_date) AS first_subscription_date,
    MIN(s.plan) AS initial_plan,
    MAX(
        CASE
            WHEN s.plan IN ('Premium', 'Family')
            THEN s.start_date
        END
    ) AS upgrade_date
FROM users u
JOIN subscriptions s
    ON u.user_id = s.user_id
WHERE u.signup_date >= '2024-01-01'
  AND u.signup_date < '2024-02-01'
GROUP BY u.user_id, u.signup_date
HAVING
    MIN(CASE
        WHEN s.start_date = (
            SELECT MIN(s2.start_date)
            FROM subscriptions s2
            WHERE s2.user_id = u.user_id
        )
        THEN s.plan
    END) = 'Basic'
    AND MAX(
        CASE
            WHEN s.plan IN ('Premium', 'Family')
            THEN 1
            ELSE 0
        END
    ) = 1
ORDER BY upgrade_date;



-- Q10 - CLIFFHANGER COMEBACKS

WITH comeback_events AS (
    SELECT DISTINCT
        w1.user_id,
        w1.show_id,
        w1.session_date AS incomplete_date
    FROM watch_sessions w1
    JOIN watch_sessions w2
        ON w1.user_id = w2.user_id
        AND w1.show_id = w2.show_id
        AND w2.session_date > w1.session_date
        AND DATEDIFF(w2.session_date, w1.session_date) BETWEEN 1 AND 7
    WHERE w1.completed = 0
      AND w1.user_id IS NOT NULL
)

SELECT
    COUNT(*) AS total_comeback_events,
    (
        SELECT ce.show_id
        FROM comeback_events ce
        GROUP BY ce.show_id
        ORDER BY COUNT(*) DESC
        LIMIT 1
    ) AS top_comeback_show_id,
    (
        SELECT s.title
        FROM shows s
        WHERE s.show_id = (
            SELECT ce.show_id
            FROM comeback_events ce
            GROUP BY ce.show_id
            ORDER BY COUNT(*) DESC
            LIMIT 1
        )
    ) AS top_comeback_show_title
FROM comeback_events;


-- Q11 - CONSECUTIVE-WEEK ENGAGEMENT

WITH weekly_activity AS (
    SELECT DISTINCT
        user_id,
        YEARWEEK(session_date, 3) AS week_key
    FROM watch_sessions
    WHERE user_id IS NOT NULL
),

numbered_weeks AS (
    SELECT
        user_id,
        week_key,
        ROW_NUMBER() OVER (
            PARTITION BY user_id
            ORDER BY week_key
        ) AS rn
    FROM weekly_activity
),

streak_groups AS (
    SELECT
        user_id,
        week_key,
        week_key - rn AS streak_group
    FROM numbered_weeks
),

streaks AS (
    SELECT
        user_id,
        streak_group,
        COUNT(*) AS streak_length
    FROM streak_groups
    GROUP BY
        user_id,
        streak_group
),

qualified_streaks AS (
    SELECT
        user_id,
        streak_length
    FROM streaks
    WHERE streak_length >= 4
)

SELECT
    COUNT(DISTINCT user_id) AS users_with_4plus_week_streak,
    MAX(streak_length) AS longest_streak_weeks,
    (
        SELECT user_id
        FROM qualified_streaks
        ORDER BY streak_length DESC, user_id
        LIMIT 1
    ) AS user_with_longest_streak
FROM qualified_streaks;


-- Q12 - CHURN SIGNAL DETECTION

WITH monthly_watch AS (
    SELECT
        user_id,
        SUM(
            CASE
                WHEN session_date >= '2024-05-01'
                 AND session_date < '2024-06-01'
                THEN watch_minutes
                ELSE 0
            END
        ) AS may_watch_minutes,

        SUM(
            CASE
                WHEN session_date >= '2024-06-01'
                 AND session_date < '2024-07-01'
                THEN watch_minutes
                ELSE 0
            END
        ) AS june_watch_minutes

    FROM watch_sessions
    WHERE user_id IS NOT NULL
      AND session_date >= '2024-05-01'
      AND session_date < '2024-07-01'

    GROUP BY user_id
),

churn_signals AS (
    SELECT
        mw.user_id,
        u.name,
        mw.may_watch_minutes,
        mw.june_watch_minutes,

        ROUND(
            (
                (mw.may_watch_minutes - mw.june_watch_minutes)
                * 100.0
                / mw.may_watch_minutes
            ),
            2
        ) AS drop_percentage

    FROM monthly_watch mw
    JOIN users u
        ON mw.user_id = u.user_id

    WHERE mw.may_watch_minutes > 0
      AND mw.june_watch_minutes <= mw.may_watch_minutes * 0.5
)

SELECT
    user_id,
    name,
    may_watch_minutes,
    june_watch_minutes,
    drop_percentage,
    (SELECT COUNT(*) FROM churn_signals) AS total_churn_signal_users
FROM churn_signals
ORDER BY drop_percentage DESC;