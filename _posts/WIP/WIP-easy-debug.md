How to make it easier to debug and how I wish app I debug would be written.

- Don't shallow Exceptions
- Wrap them
- Add context of where it have been thrown
- Add context on how to get there (log enough data so that you can reproduce the issue)
    - Which user, when, all the data relevant (id of the objects being treated, parameters, ...)
- Screenshot of the app during exception can help a lot to get more context