# lab-4

## Timers

Build times for specific actions.

  * cf-mysql-deployment (60 mins)

### Errands



  ```
  $ bosh -d cf-mysql errands
  ```

  ```
  $ bosh -d cf-mysql run-errand broker-registrar
  ```

  ```
  $ bosh -d cf-mysql run-errand smoke-tests
  ```
