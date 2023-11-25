How to make it easier to debug and how I wish app I debug would be written.

Hmmm, all of that can be resume by "use logs", with alert system and extensive context.

- Don't shallow Exceptions
- Wrap them
- Add context of where it have been thrown
- Add context on how to get there (log enough data so that you can reproduce the issue)
    - When
    - Where (identify from where in the code this info have been written)
    - Who
    - What (all the data relevant, id of the objects being treated, parameters, ...)
    - May want to add severity as well
- Screenshot of the app during exception can help a lot to get more context
- Use standard format for logs ()