# trace_lab

1. Testing the Etherscan Registration form
a. Create TEST SCENARIOS for the registration form on Etherscan
website (https://etherscan.io/register)
b. Explain what type of tools you would use to enable an automatic
testing system for the task above and how they would be utilized.
c. Create E2E browser tests that will cover all test scenarios.

2. Create a UNIT test for the class below:

const REPLICATION_STATUSES = {
pending: 'PENDING',
initialized: 'INITIALIZED',
failed: 'FAILED'
}
const maxNumberOfReplicationRetries = 5;
class ApiClient {
/**
* Method used for fetching replication status from the provided endpoint
* @param replicationHandlerId - string
* @param endpoint - url string
* @param webClient - client for sending get requests to provided endpoint
* @returns response from get request
* @private
*/
async _getReplicationResult(replicationHandlerId, endpoint, webClient) {
if (!replicationHandlerId) {
throw Error('Unable to start replication, handler id undefined.');
}
let replicationResponse = {
status: REPLICATION_STATUSES.pending
}
let retries = 0;
do {
retries++;
await this.sleepForMilliseconds(5 * 1000);
try {
/* expected response format
{
data: {}
status: String
}
* */
replicationResponse = await webClient.get(
endpoint
)
logger.debug(`Replication result status: ${replicationResponse.data.status}`);
} catch (e) {
if (retries === maxNumberOfReplicationRetries) {
throw e;
}
}
} while (replicationResponse.data.status === REPLICATION_STATUSES.pending ||
replicationResponse.data.status === REPLICATION_STATUSES.initialized);
if (replicationResponse.data.status === REPLICATION_STATUSES.failed) {
throw Error(`Replication failed. Reason: ${replicationResponse.data.message}.`);
}
return replicationResponse.data
}
}
async sleepForMilliseconds(milliseconds) {
await new Promise(r => setTimeout(r, milliseconds));
}
