# Request Merchandise Authorization (RMA) notification structure design

## Intent

Structure for RMA process.

## Definitions

- Consumer : Customers of device vendors (Eg: Google, Microsoft, X).
- Publisher: Device vendor (Ciena, Adva, Infinera, X).

## Features 

RMA case is raised by the consumer to request the replacement of the defective parts. It consists of the following subflows:
1. Consumer starts to request the RMA from the Publisher, and wait for the review/approval from the publisher
1. Publisher ships the replacement, and notify the consumer about the shipment status of the replacement
1. Consumer request techincial dispatch/schedule if needed
1. Consumer ships the defective part for failure analysis, and notify the consumer about the shipment status of the defective parts
1. Publisher notifies the failure analysis report to consumer
## Out of scope

We would not be discussing the transport methodologies preferred for this notification message.

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

### Review/Approval 

At this stage, Publisher will validate the required information for the RMA case, and approve / reject / request more information, which may include:
   * whether any mandotary information is missing
   * whether a kind of service level is supported for specific region

### Parts tracking 

There are two seperate flows at this stage:
   1. Replacement shippment:
      1. Publisher will provide the shippment information, including carrier, planned arrive date 
      1. Publisher will notify the status of the replacement shippment and delivery
   2. Defective part shippment:
      1. Consumer will provide the shippment information, including carrier, planned arrive date 
      1. Publisher will notify the status of the replacement shippment and delivery

### Notification

One RMA case is dedicated to one part with one unique identifier, e.g., serial number. Each RMA involves multiple communication messages between the publisher and consumer. Each RMA case has a unique ID.

## Notification structure

### General format

`field_name:field_type:description`

### Field Details

#### case_id

Identifier for a RMA case which dedicated to a part with a unique identifier. 

`case_id:string:"RMA case identifier"`


#### case_status

RMA case status 
`case_status:enum value:"stage of RMA case" enumeration: [OPEN, APPROVED, REJECTED]`


#### case_status_time

Epoch time when the status is updated from Publisher, we have one specific time for each status listed in `case_status`

`case_open_time:date time:"Unix epoch time when consumer starts to request the RMA"`
`case_approved_time:date time:"Unix epoch time when publisher approve to request the RMA"`
`case_rejected_time:date time:"Unix epoch time when publisher reject the RMA"`

#### part_shippment_status 

Replacement / defective part shippment status

`service_type:enum value:"Type of service: leased wave/dark fiber" enumeration: [LEASED_WAVE, DARK_FIBER]`

#### part_type

Replacement or defective parts

#### service_impact

Type of impact caused on the service because of the planned work.

`service_impact:enum value:"Type of service impact." enumerations: [COMPLETE_OUTAGE, POSSIBLE_OUTAGE, INTERMITTENT_OUTAGE]`

### impact_type

Type of impact for the maintenance. If the outage would be complete, intermittent or possible.

`impact_type:enum value:"If this impact is primary or secondary for a planned maintenance." enumerations: ['COMPLETE_OUTAGE', 'POSSIBLE_OUTAGE', 'INTERMITTENT_OUTAGE']`

### impact_satus

Status of the work.

`impact_satus:enum value:"Status of the impact." 
enumerations: [SCHEDULED, IN_PROGRESS, COMPLETED, CANCELED]
`

### impacts_related

If there are other impacts related to the impact in notification, those could be listed here.

`impact_type:repeated int:"Impacts related to the impact in the notification."`

### physical_packet_link_info

Physical packet link affected because of the change, this is for leased wave notifications.

#### physical_packet_link_id

Circuit ID of a link. ID could be stored independently at consumer and publisher. It would be ideal to have these matching between the two, but not necessary.

`physical_packet_link_id:string:"Circuit identifying router port endpoints"`

#### affected_capacity

Link capacity (bps) affected because of the work.

`affected_capacity:integer:"Affected traffic in bps"`

### physical_packet_links_info

If multiple links are affected because of the change, this field collects them together.

` physical_packet_link_info:list of physical_packet_link_info:""

`

### ots_info

Information regarding the Optical Transport Section. This is populated for a fiber maintenance.

#### ots_id

Fiber strand identifier.

`ots_id:string:"Identifier for Optical Transport Section."`

### ots_affected

`ots_affected:repeated ots_info:"OTS affected because of the maintenance window."`

### oms_info

OMS information.

#### oms_id

OMS Identifier. `oms_id:string:"Identifier for Optical Multiplexing Section"`

#### oms_affected

oms_affected:list of oms_info:"OMS details regarding the maintenance"

### close_code

Status of the maintenance work closure.

` 
close_code:enum value:"Close code of the maintenance work under the impact" 
enumerations: ['SERVICE_RESTORED', 'SERVICE_RESTORED_WITH_ISSUES', 'SERVICE_NOT_RESTORED', 'SERVICE_NOT_AFFECTED']

`


## Notification structure
List of required fields for a notification

For leased wave and fiber maintenance notification
- maintenance_id
- impact_id
- impact_start_time
- impact_end_time
- impact_type
- impact_status

For leased wave notifications 
- physical_packet_link_info.physical_packet_link_id

For dark fiber notifications
- ots_info.ots_id

### Fields' Table
TBA

