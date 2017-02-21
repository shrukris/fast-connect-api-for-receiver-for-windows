#API examples

A simple command-line driven test program is provided in the same .zip
file as this document as a sample to demonstrate the usage of these
APIs. Below are some code snippets from the sample.

##Example 1: Log on a user

The following code provides user credentials to SSO to log on a user.

```c
BOOL DoLogonSsoUser(const wchar_t* pUsername, const wchar_t * pPassword, const wchar_t *pDomain)
	MSV1_0_INTERACTIVE_LOGON msv1Logon;
	
	if( !pUsername || ! pPassword || !pDomain) 
		return FALSE;
  sizeof(wchar_t));
	msv1Logon.UserName.Buffer = (PWSTR)pUsername;
	
	msv1Logon.Password.Length = (USHORT)(wcslen(pPassword) * 
  sizeof(wchar_t));
	msv1Logon.Password.Buffer = (PWSTR)pPassword;
	
	msv1Logon.LogonDomainName.Length = (USHORT)(wcslen(pDomain) * 
  sizeof(wchar_t));
	msv1Logon.MessageType = MsV1_0InteractiveLogon;
	
	printf("calling LogonSsoUser...\n");
	
	SecureZeroMemory(&msv1Logon, sizeof(msv1Logon));
	if(rc != LOGONSSOUSER_OK) {
		return FALSE;
	}
	printf("\n User logged on. Result of LogonSsoUser(): %d\n", result); 
	return TRUE;
##Example 2: Log on a user with a smart card

The following code provides user credentials to SSO to log on a user with a smart card.

```c
BOOL DoLogonSsoUserWithPin(const wchar_t* pin)
	if (InitLibrary())
		printf("calling LogonSsoUserWithPin...\n");
			return FALSE;
	return FALSE;
	
##Example 3: Log off a user

This function performs a logoff from an SSO perspective, removing the user’s credentials from the system for the purposes of future SSO-based authorization. Existing authorized sessions can still be used. If the previous user was not logged off, that user’s credentials are restored (see example 4).

ICO/CCM interfaces can be used to perform full logoff/disconnect.

```c
void DoLogoffSsoUser()
	 printf("\n User logged off\n");
```

##Example 4: Managing an exclusive endpoint

If the current user is not logged off and a new user is logged on, the previous user’s credentials are saved by the system to be restored when the current user logs off. Only a single level of restore is supported. Logging off a second time without an intervening logon will result in clearing the SSO credentials and will require authentication before launching new sessions. This supports the following scenario using Controller, which is a custom application using `CtxCredApi`, `ICO`:

1. A kiosk is auto-logged on with a default/generic user (could be the normal Windows logon or Controller calls `LogonSsoUser()`)
2. The kiosk is then available with generic credentials for customer use.
3. Sales Rep A logs on with his own credentials:
	a.  Controller calls `LogonSsoUser()`.
	a. Controller calls `LogoffSsoUser()`, removing Sales Rep A’s credentials and restoring the generic user.

This scenario can be repeated indefinitely.

##Example 5: Managing user restore and shared endpoints

If the current user is not logged off and a new user is logged on, the previous user’s credentials are saved by the system to be restored when the current user logs off. Only a single level of restore is supported. Logging off a second time without an intervening logon will result in clearing the SSO credentials and will require authentication before launching new sessions. This supports the following scenario using Controller, which is a custom application using CtxCredApi, ICO:

1. A nurse logs on normally and launches sessions. (Controller calls `LogonSsoUser()` or could be a Windows logon).
5. The nurse resumes as at Step 1.


##Example 6: Checking for reconnecting sessions

Some applications may need to determine whether sessions are being reconnected before launching new instances. The ICO interface, ICACLient::EnumerateCCMSessions(), reports the IDs of the ICA sessions that already exist and that are being formed. This interface can be used by the caller to heuristically determine whether new ICA sessions are being formed, for example after Receiver is restarted and is automatically reconnecting disconnected sessions.


```c
for (int i = 0; i < 40000; i += 2000)
	{
		printf("IsReconnectInProgress %d seconds left to wait\n", (40000 - i) /
1000);
		BOOL bReconnectInProgress = FALSE;
		DWORD status = pfnIsReconnectInProgress(2000,
&bReconnectInProgress);
		if (status == ERROR_SUCCESS)
		{
			if (bReconnectInProgress)
			 {
				printf("IsReconnectInProgress: Reconnect in Progress\n");
			 }
			 else
			 {
			 	printf("IsReconnectInProgress: No Reconnect\n");
			 }
			 break;
		}
		else if (status == ERROR_TIMEOUT)
		{
			printf("IsReconnectInProgress: TIMEOUT\n");
		}
		else
		{
			printf("IsReconnectInProgress: Error: %d\n", status); 
			break;
		}
	}
```