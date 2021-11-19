# Request Merchandise Authorization (RMA) notification structure design

## Intent

Structure for RMA process.

## Definitions

- Consumer : Customers of device vendors (Eg: Google, Microsoft, X).
- Publisher: Device vendor (Ciena, Adva, Infinera, X).

## Features 

RMA case is raised by the consumer to request the replacement of the defective parts. It consists of the following subflows:

1. Consumer starts to request the RMA from Publisher
1. Consumer waits for the review from the Publisher
1. Consumer responds any pending review request from the Publisher till RMA is approved
1. Publisher approves the RMA request
1. Consumer waits for RMA shipment update from the Publisher
1. Publisher ships the replacement, and notify the consumer about the shipment status of the replacement
1. Consumer triggers internal processes to receive shipped RMA part.
1. Consumer completes the RMA work with internal processes.
1. Consumer request techincial dispatch/schedule if needed
1. Consumer ships the defective part for failure analysis, and notify the consumer about the shipment status of the defective parts
1. Publisher notifies the failure analysis report to consumer

## Out of scope

We would not be discussing the replacement/maintainance details, and any internal support tickets related to the maintainance

## Stages of RMA Lifecycle

### Creation

At this stage RMA(s) is raised by the Consumer, this might include:

1. Collect the information, including 
   * device/part basic information, e.g., serial number, part type, OS software version
   * device/part alarms and logs
   * site information where the defective parts resides
   * contact information from Consumer side
   * RMA service level, e.g., normal / escalate service

1. Consumer request for RMA from Publisher
1. Request type for each customer (email, API, manual).


Note: One RMA case is dedicated to one part/module with one unique identifier, e.g., serial number. Each RMA involves multiple communication messages between the publisher and consumer. Each RMA case has a unique ID.

### Review/Approval 

At this stage, Publisher will validate the required information for the RMA case, and approve / reject / request more information, which may include:
   1. whether any mandotary information is missing
   1. whether a kind of service level is supported for specific region

### Part Tracking

There are two seperate cases at this stage:
   1. Replacement shippment:
      1. Publisher will provide the shippment information, including carrier, planned arrive date 
      1. Publisher will notify the status of the replacement shippment and delivery
   1. Defective part shippment:
      1. Consumer will provide the shippment information, including carrier, planned arrive date 
      1. Publisher will notify the status of the replacement shippment and delivery

### Techician Schedule  (Optional)

Technician dispatch may be needed for un-manned sites

### Failure Analysis 

After Consumer ships the defective part to Publisher, publisher will collect the information and analyze the root cause of the part failure.


## Message structure

### General format

`field_name:field_type:description`

### Part Details 

#### part_id

Indentifier of the defective part or the replacement, e.g., serial number

`part_id:string:"Part id" `
#### part_type

Replacement or defective part

`part_type:enum value:"Part type" enumeration: [REPLACEMENT, DEFECTIVE_PART]`

#### part_status

whether the part is online or offline

`part_status:enum value:"whether part is online or offline" enumeration: [ONLINE, OFFLINE]`


### Problem Details

#### problem_description
Problem summary of the failed part from the Consumer

`problem_description:string value:"Problem summary"`

#### service_type

whether the part failure affects the service from the Consumer

`service_type:enum value:"service type of the part" enumeration: [IN_SERVICE, NOT_IN_SERVICE]`

#### service_impact_time

when the service start to impacted the service from the Consumer 

`service_impact_time:date time:"Unix epoch time when the service is impacted by the part failure"`


### Case Details
#### case_id

Identifier for a RMA case. 

`case_id:string:"RMA case identifier"`

#### case_status

RMA case status 

`case_status:enum value:"stage of RMA case" enumeration: [OPEN, APPROVED, REJECTED]`

#### case_status_time

Epoch time when the status is updated from Publisher, we have one specific time for each status listed in `case_status`

`case_open_time:date time:"Unix epoch time when consumer starts to request the RMA"`

`case_approved_time:date time:"Unix epoch time when publisher approve to request the RMA"`

`case_rejected_time:date time:"Unix epoch time when publisher reject the RMA"`

#### Part Shippment Details 
#### part_shippment_address

Replacement / defective part shippment status

`part_shippment_address:string value:"shippment address"`

#### part_shippment_status 

Replacement / defective part shippment status

`part_shippment_status:enum value:"shippment status of the part" enumeration: [INITIATED, SHIPPED, DELIVERED]`


### Technician Dispatch Details 

Keep consistent with Fiber Maintainance Flow 

### Failure analysis

#### failure_analysis_id

Indentifier for the failure analysis from the Publisher, unique ID for each RMA case

`failure_analysis_id:Integer:"indentifier for the failure analysis"`

#### failure_analysis_status

Status of the failure analysis from the Publisher 

`failure_analysis_status:enum value:"failure analysis status" enumerations: ['INITIATED', 'NEED_INFORMATION', 'IN_PROGRESS', 'DONE']`

#### failure_analysis_result_type

The result type of the failure analysis from Publisher, e.g., report, excel, email.

`failure_analysis_result_type:enum value:"failure analysis result type" enumerations: ['REPORT', 'EMAIL']`

#### failure_analysis_result_details

The failure analysis details from from Publisher, including failure code, root cause, impact analysis etc.

`failure_analysis_result_details:Depends on result type:"failure analysis result details dependes on the result type" `


### Case Close  

#### close_code
Status of the RMA case closure. 

`close_code:enum value:"Close code of the RMA case" enumerations: ['SUCCEED', 'INFORMATION_MISSING', 'INFORMATION_MISMATCH', 'PART_DELIVERY_FAILED', 'FAILURE_ANALYSIS_FAILED'] `
