# Fiber Maintenance Notification structure

## Intent

Structure for leased wave or dark fiber maintenance notification.

## Definitions

- Publisher: Fiber vendor (Centurylink, Zayo).
- Consumer : Customers of Fiber vendors (Eg: Google, Microsoft, X).
- Consumer NOC: Could be humans or automation framework.
- OMS: Optical Multiplexing Section.
- OTS: Optical Transport Section.

## Need

Fiber vendor maintenance notification is processed by NOC to check for services affected related to the maintenance. This check is performed multiple times during a maintenance life cycle to verify production impact of a particular vendor maintenance.

Eg lifecycle:

1. Consumer NOC receives notification regarding a leased wave maintenance.
2. Consumer NOC binds Publisher's notification to set of affected Consumer router ports.
3. Consumer NOC schedules planned work for the maintenance start and end times.
4. Consumer NOC drains services before the start time.
5. Publisher sends notification about maintenance completion with status.
6. Consumer NOC checks the maintenance status at the end time (assuming publisher reports maintenance completed fine).
7. Consumer NOC un-drains services if no issues found.
8. Consumer NOC engages with publisher if issues are observed.
9. Operational metrics are communicated back and forth (via email or software integrations) to resolve any issues.

The entire consumer NOC operational process is around the maintenance notification. If this is structured uniformly across vendors it can scale operational automation.

## Out of scope

We would not be discussing the transport methodologies preferred for this notification message.

## Stages of Maintenance Lifecycle

### Creation

At this stage a particular maintenance is created by the Publisher and is in the planning phase, this might include:

1. Compiling customers affected by the maintenance (list of customers affected).
2. Notification type for each customer (email, API, manual).
3. Verifications for the maintenance.
4. Procuring resources for the maintenance.

### Scheduling

Once resources are procured and the maintenance is ready to be scheduled, the maintenance is scheduled. At this stage the first set of notifications are sent to the consumers. Major contributors to scheduling are the start and end times of the planned work. The maintenance can be scheduled for single or multiple days, use cases for this are:

1. Maintenance spanning multiple days.
2. Maintenance planned with backup windows.

### Maintenance Details

This denotes the type of service and the type of service impact for the maintenance.

1. Dark fiber or Leased Wave.
2. Complete Outage, Possible Outage, Intermittent outage.

### Operational information

Circuit and Fiber identifiers are stored at the publisher and consumer ends in resepctive databases. These are used to identify the impacted services and used for notifying the consumer.

NOC checks set of metrics related to the affected services For maintenance post checks consumer. If the publisher and consumer can exchange these metrics between each other, it enables operational automation at both ends for pre/post maintenance work.

#### Affected Paths

Circuit identifiers at each end. Consumer and Publisher store the circuits in a datastore at their respective ends.

#### Component metrics

Network component operational metrics related to the maintenance.

Eg :

- light levels Tx/Rx
- Performance monitoring Data
- Errors
- Flaps

### Notification

Each maintenance involves multiple communication messages between the publisher and consumer. Each notification has a unique ID for a particular maintenance ID.

## Notification structure

### Format

`field_name:field_type:description`

### Field Details

#### maintenance_id

Identifier for a planned work. Single maintenance ID can have multiple scheduled work days. Each maintenance_id is unique to a single planned work.

`notification_id:integer:"Notification identifier"`

#### notification_time

Epoch time when the notification was sent by the Publisher.

`notifcation_time:integer:"Unix epoch time when notification was sent"`

#### service_type

Type of service the notification is pertaining to. Possible values: Leased Wave, Dark Fiber

`service_type:enum value:"Type of service: leased wave/dark fiber" enumeration: [LEASED_WAVE, DARK_FIBER]`

#### service_impact

Type of impact caused on the service because of the planned work.

`service_impact:enum value:"Type of service impact." enumerations: [COMPLETE_OUTAGE, POSSIBLE_OUTAGE, INTERMITTENT_OUTAGE]`

#### impact_id

Single impact window for the planned work. If there are multiple windows planned for a maintenance_id, this field distinguishes notification for each window.

`impact_id:integer:"Impact identifier"`

### impact_start_time

Epoch time when the impact starts.

`impact_start_time:integer:"Unix epoch time when impact is planned to start."`

### impact_end_time

Epoch time when the impact ends.

`impact_end_time:integer:"Unix epoch time when impact is planned to end."`

### impact_type

Type of impact for the maintenance. If the outage would be complete, intermittent or possible.

`impact_type:enum value:"If this impact is primary or secondary for a planned maintenance." enumerations: ['COMPLETE_OUTAGE', 'POSSIBLE_OUTAGE', 'INTERMITTENT_OUTAGE']`

### impact_type

If the impact is primary/secondary. There might be multiple windows scheduled for a maintenance work and these could be for a single planned maintenance. This field distinguishes if a notification is for primary planned window or secondary days which might be backup for the same work.

`impact_type:enum value:"Type of impact on service." enumerations: [PRIMARY, SECONDARY]`

### impact_satus

Status of the work.

`impact_satus:enum value:"Status of the impact." 
enumerations: [SCHEDULED, IN_PROGRESS, COMPLETED, CANCELED]
`

### impact_primary_id

This is populated when the impact is associated to another impact. the value of primary impact_id.

`impact_primary_id:integer:"Impact ID of the primary work for the planned maintenance."`

### impact_secondary_id

This is populated when the impact_id has secondary impact_ids planned.

`impact_secondary_id:list of integers:"Impact ID of the secondary work for the planned maintenance."`

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

### Fields' Table
TBA
