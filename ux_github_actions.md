# GitHub Actions

A series of GitHub Actions help automate working with the NR Broker.

* [Intention open](https://github.com/bcgov-nr/action-broker-intention-open)
* [Action start](https://github.com/bcgov-nr/action-broker-action-start)
* [Vault login](https://github.com/bcgov-nr/action-broker-vault-login)
* [Vault provision](https://github.com/bcgov-nr/action-broker-vault-provision)
* [Vault token revoke](https://github.com/bcgov-nr/action-broker-vault-revoke)
* [Action end](https://github.com/bcgov-nr/action-broker-action-end)
* [Intention close](https://github.com/bcgov-nr/action-broker-intention-close)

## Linux Scripting

These actions double as a working demonstration of calling the Broker API. The scripts in each to the actions should work on most Linux distributions. You can copy the scripts into a container or onto a Linux server and use them outside of a GitHub action. 

## Accessing Action

**Example**: Run Liquibase, GitHub Action, Other one-off or scheduled activities

If you are following the access workflow, you'll be using the role id (provided) to retrieve a wrapped token with access to Vault. The token will have policies that are identical to a provisioned token. The 'provision/token/self' API allows you to skip sending a request to Vault to do the login yourself. The main difference is that the token cannot be renewed. The example here assumes some kind of database upgrade is going on.

The first step is to open an intention with the Broker API. The open API is authenticated using a Broker Account that is connected to the project/service(s) in the actions. Your team can generate a Broker Token for Broker Accounts that your team is connected to. See: [Broker Account Token](dev_account_token.md)

You must modify the event, service and user fields in this example. In particular, the user id (<username>@idir or <username>@github) must be a member of your team (even if this sent by an automated process). Jq is an excellent tool for doing this modification.

You may wish (or be required) to provide additional actions to describe the event in more detail.

A periodic process that is accessing secrets may be transient. This means there is no need to save the event long-term and can be filtered out from other activites. If your activity is transient then event.transient should be set to true.

**Call   1.** [Intention open](https://github.com/bcgov-nr/action-broker-intention-open)

```
name: Open intention
        uses: bcgov-nr/action-broker-intention-open@v2
        with:
          broker_jwt: ${{ secrets.<%= brokerJwt %> }}
          intention_path: intention.json
          quickstart: true
```
Retrieving secrets should be through GitHub Actions and ***NOT*** Provisioning. Provisioning can lead to security risks as provisioned tokens can be renewed and last indefinitely. On the other hand, GitHub Actions provide tokens that are non-renewable and have ajustable TTL (time-to-live), ensuring the tokens disappear after a set amount of time. ***For each pod to have it's own token, provisioning should happen in startup(OpenShift), and not in deploy(GitHub Action)***. 


**Call  2.** [Vault login](https://github.com/bcgov-nr/action-broker-vault-login)

```
- uses: bcgov-nr/action-broker-vault-login@v2
  with:
    # The token of the action containing the service to use to login as
    action_token: ''

    # The broker url.
    # Default: 'https://broker.io.nrs.gov.bc.ca'
    broker_url: ''

    # The service's application role id in vault. Setting this is recommended to avoid environment mismatch.
    role_id: ''

    # The vault url.
    # Default: https://knox.io.nrs.gov.bc.ca
    vault_url: ''

    # If the token should be returned wrapped. This must be set to true if the
    # action will send the token to an external system.
    # Default: false
    wrap_token: ''
```


**Call  3.** [Vault token revoke](https://github.com/bcgov-nr/action-broker-vault-revoke)

```
- uses: bcgov-nr/action-broker-vault-revoke@v2
  with:
    # The vault token to revoke
    vault_token: ''

    # The vault url.
    # Default: https://knox.io.nrs.gov.bc.ca
    vault_url: ''
```


**Call  4.** [Intention close](https://github.com/bcgov-nr/action-broker-intention-close)

```
 - name: Close intention
        if: ${{ success() && env.INTENTION_TOKEN != '' }}
        uses: bcgov-nr/action-broker-intention-close@v3
        with:
          intention_token: ${{ env.INTENTION_TOKEN }}
      - name: Close intention (Failure)
        if: ${{ failure() && env.INTENTION_TOKEN != '' }}
        uses: bcgov-nr/action-broker-intention-close@v3
        with:
          intention_token: ${{ env.INTENTION_TOKEN }}
          outcome: failure
```
