Here is a simple example of how to use this API.

```php

    //...

    function subscribeToAllLists($email, $name)
    {
        $lists = Injector::inst()->get(MyCampaignMonitor::class)->getLists();
        foreach($lists as $list) {
            Injector::inst()->get(MyCampaignMonitor::class)
                ->addSubscriber($email, $name, $list)
        }
    }

    //...

```

```php


namespace MyCompany\MyWebsite\Api;

use Sunnysideup\CampaignMonitorApi\Api\CampaignMonitorAPIConnectorBase;

class MyCampaignMonitor extends CampaignMonitorAPIConnectorBase
{

    /**
     * Gets all subscriber lists the current client has created.
     *
     * @return mixed A successful response will be an object of the form
     *               array(
     *               {
     *               'ListID' => The id of the list
     *               'Name' => The name of the list
     *               }
     *               )
     */
    public function getLists()
    {
        //require_once '../../csrest_clients.php';
        require_once BASE_PATH . '/vendor/campaignmonitor/createsend-php/csrest_clients.php';
        $client_id = Environment::getEnv('SS_CAMPAIGNMONITOR_CLIENT_ID');
        if(! $client_id) {
            $client_id = $this->Config()->get('client_id');
        }

        $wrap = new \CS_REST_Clients($this->Config()->get($client_id), $this->getAuth());
        $result = $wrap->get_lists();

        return $this->returnResult(
            $result,
            'GET /api/v3.1/clients/{id}/lists',
            'Got Lists'
        );
    }

    public function addSubscriber(
        string $email,
        string $name,
        string $listID
    ) {
        //require_once '../../csrest_subscribers.php';
        require_once BASE_PATH . '/vendor/campaignmonitor/createsend-php/csrest_subscribers.php';
        $wrap = new \CS_REST_Subscribers($listID, $this->getAuth());
        $customFields = $this->cleanCustomFields([]);
        $request = [
            'EmailAddress' => $email,
            'Name' => $name,
            'CustomFields' => [],
            'Resubscribe' => true,
            'RestartSubscriptionBasedAutoResponders' => true,
            'ConsentToTrack' => 'Unchanged',
        ];
        $result = $wrap->add(
            $request
        );

        return $this->returnResult(
            $result,
            'POST /api/v3.1/subscribers/{list id}.{format}',
            'Subscribed with code ...'
        );
    }
}



```


### more examples:

please visit: https://github.com/sunnysideup/silverstripe-campaignmonitor/tree/master/src/Api/Traits
