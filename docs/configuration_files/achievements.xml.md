# Achievements.xml

This file configures "achievements". It consists of multiple `achievement` nodes, in the usual roar config wrapper. Currently the only way to obtain an achievement is through completing [tasks](tasks.xml.md).

~~~
<chameleon_data>
  <achievements>
    <achievement ... />
  </achievements>
</chameleon_data>
~~~


An example entry looks like this:

~~~
<achievement
  ikey="one_down"
  steps="5"
  label="One Down"
  description="Earned by mastering at least one stage in New York"
  type="complete_job"
  job_ikey="grow_your_family"
/>
~~~

## `ikey` (string)
Each achievement has its own unique *ikey*.

## `label` (string)
A short identifying label for the achievement.
This is cosmetic only.

## `description` (string)
A longer description of the achievement.
This is cosmetic only.

## `type` (string)
The only currently supported value is `complete_job`. I.e. the achievement is granted once the player has completed the specified task the required number of times.

## `job_ikey` (string)
Then *ikey* of the [task](tasks.xml.md) that must be completed to obtain the achievement.

## `steps` (integer)
Number of times the task must be completed to obtain the achievement.

