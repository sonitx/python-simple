# Production system

## Global
- Insight: https://insight.tmsapp.net/
- Jenkins: https://jenkins.salespikes.global/
- Logs monitor: https://logging.salespikes.global/login?next=%2F
- Grafana monitor app: https://monitoring.salespikes.global/login
- Grafana monitor database: http://27.71.226.239:9043/d/000tqgahs/postgresql-all?orgId=1&refresh=30s
- Dashboard monitor cronjob: https://jobs.monitor.salespikes.global/

## Geo Malaysia 1 (or Malaysia - both Fresh and Resell leads)
- org_id: 11
- Geo code: MY
- TMS: http://ekwmy.tmsapp.net/
- System Management (SMG): https://smgr-my.tmsapp.net/
- Rescue system (RMS): https://rescue-my.tmsapp.net/#/login
- WFM: https://mywfm.tmsapp.net/

## Geo Indonesia 2 (This geo for Fresh Lead only)
- org_id: 8
- Geo code: ID2
- TMS: https://idgms.tmsapp.net/auth/login
- System Management (SMG): https://idgms-smgr.tmsapp.net/account/login
- Rescue system (RMS): https://idgms-rescue.tmsapp.net/login
- WFM: https://idwfm.tmsapp.net/

## Geo Indonesia 3 (This geo for Resell Lead only)
- org_id: 18
- Geo code: ID3
- TMS: https://idcms.tmsapp.net/auth/login
- System Management (SMG): https://id3-smgr.tmsapp.net/account/login
- Rescue system (RMS): https://idcms-cem.tmsapp.net/
- WFM: https://idwfm.tmsapp.net/

## Geo Thailand 1 (or Thailand - This geo for Resell Lead only)
- org_id: 10
- Geo code: TH
- TMS: http://eftth.tmsapp.net/auth/login
- System Management (SMG): https://smgr-th.tmsapp.net/account/login
- Rescue system (RMS): https://eftth.tmsapp.net/cs/#/rescuejob
- WFM: https://thwfm.tmsapp.net/

## Geo Thailand 4 (or Thailand - This geo for Fresh Lead only)
- org_id: 20
- Geo code: TH4
- TMS: https://thltl.tmsapp.net/auth/login
- System Management (SMG): https://thltl-smgr.tmsapp.net/
- Rescue system (RMS): https://thltl-rescue.tmsapp.net/
- WFM: https://thwfm.tmsapp.net/

## Geo India 1 (or India - both Fresh and Resell leads)
- org_id: 19
- Geo code: IN
- TMS: https://inpsw.tmsapp.net/auth/login
- System Management (SMG): https://in1-smgr.tmsapp.net/account/login
- Rescue system (RMS): https://in1-rescue.tmsapp.net/login
- WFM: https://inwfm.tmsapp.net/

## Geo Peru (both Fresh and Resell leads)
- org_id: 31
- Geo code: PE
- TMS: https://ltznh.tmsapp.net/auth/login
- System Management (SMG): https://ltznh-smgr.tmsapp.net/account/login
- Rescue system (RMS): https://ltznh-cem.tmsapp.net/
- WFM: https://ltznh-wfm.tmsapp.net/

## Geo Colombia (both Fresh and Resell leads)
- org_id: 32
- Geo code: CO
- TMS: https://ltznh.tmsapp.net/auth/login
- System Management (SMG): https://ltznh-smgr.tmsapp.net/account/login
- Rescue system (RMS): https://ltznh-cem.tmsapp.net/
- WFM: https://ltznh-wfm.tmsapp.net/

# IT Support level 1

## TMS system

- Question 1: User cannot login to TMS
- Answer 1: Go to SMG system -> User -> Users -> Select user & change password

- Question 2: TMS load slowly
- Answer 2: check case by case:
  + Agent/Sale team recheck their PC/Laptop network
  + Restart service 01_api, 01_valid
  + Check workload of database (go to Grafana monitor database)

- Question 3: Agent can not get fresh/resell lead
- Answer 3: check case by case:
  + Check agent is added to group or not on SMG
  + Agent logout and login again
  + Check workload of database (go to Grafana monitor database)

- Question 4: Agent can not make call from TMS
- Answer 4: check case by case:
  + Restart 06_pbx first
  + If not make call, check log with keyword "Call Response", if log shows NULL or ERROR, contact with Call Center contact point (go to Logs monitor)

- Question 5: Agent can not save lead status (duplicate key value error, …)
- Answer 5: check case by case:
  + Restart 01_api and waiting about 30s for restart service
  + If still error, contact with L2

- Question 6: User want to add more address
- Answer 6: 
  + If user want to create new master province
  ```sql
  INSERT INTO lc_province(prv_id, name, shortname, code, status)
  VALUES ('province_id_int', 'province_name', NULL, NULL, 0) RETURNING prv_id as province_id;
  ```
  + If user want to create new master district
  ```sql
  INSERT INTO lc_district (dt_id, prv_id, name, shortname, code, dcsr, status) 
  VALUES(nextval('dt_id_sequence'::regclass), 'province_id_int', 'district_name', null, null, NULL, 0) returning dt_id as district_id, name as district_name;
  ```
  + If user want to create new master ward/subdistrict
  ```sql
  INSERT INTO lc_subdistrict(sdt_id, dt_id, province_id, name, shortname, status)
  VALUES (nextval('sdt_id_sequence'), 'district_id_int', 'province_id_int', 'ward_name', 'ward_short_name', 0);
  ```
  + If user want to create new master neighborhood
  ```sql
  INSERT INTO lc_neighborhood(id, sdt_id, province_id, district_id, name, code, status)
  VALUES (nextval('nh_id_sequence'::regclass), 'ward_id_int', 'province_id_int', 'district_id_int', 'neighborhood_name', 'neighborhood_code', 0);
  ```

- Question 7: There is no new Fresh lead
- Answer 7: 
  + Restart 04_agency-connect , 01_batchjob
  + If still no fresh lead, contact with L2

- Question 8: Supervisor can not reassign lead to agent
- Answer 8: check case by case:
  + Restart 01_valid
  + Check agent must be same team with Supervisor

- Question 9: Supervisor want to add new team
- Answer 9: run SQL 
  ```sql
  INSERT INTO or_team (id, name, enable, manager_id, team_type) 
  VALUES ((select max(id) + 1 from or_team ),'{team_name}', 't', {manager_id}, 210); 
  ```

- Question 10: Supervisor cannot add agent to group
- Answer 10: Click some times maybe due to duplicate key

- Question 11: User request to remove lead from order list
- Answer 11: Run below SQL
  ```sql
  Update cl_callback set is_deleted = 1,modifydate = now() where lead_id in({lead_id}) ;
  update cl_fresh set agent_hold = null where lead_id={lead_id} and org_id={org_id};
  ```

- Question 12: Update user's role to manager
- Answer 12: Run below SQL
  ```sql
  BEGIN;
  UPDATE or_user SET user_type='manager' WHERE user_id={user_id};
  UPDATE or_user_role SET role_id=16 WHERE user_id={user_id};
  COMMIT;
  ```

- Question 13: Update user's role to leader
- Answer 13: Run below SQL
  ```sql
  begin;
  update or_user_role set role_id = 15 where user_id in ({user_id});
  update or_user set user_type = 'team leader' where user_id in ({user_id});
  commit;
  ```

- Question 14: Error notification after login
- Answer 14: Run below SQL
  ```sql
  begin;
  UPDATE or_user SET is_notification_enabled = false where user_id={user_id};
  commit;
  ```

- Question 15: Change payment method of SO from COD to Bank Transfer
- Answer 15: Run below SQL
  ```sql
  UPDATE od_sale_order SET payment_method = 2, modifydate = NOW() WHERE so_id = {so_id} AND org_id = {org_id};
  UPDATE od_do_new SET amountcod = 0 WHERE so_id = {so_id} AND org_id = {org_id};
  ```

- Question 16: Error create DO to FFM due to stock not enough
- Answer 16: Ask logistic check with fulfillment partner

- Question 17: Error create DO to LM due to wrong address
- Answer 17: Ask logistic check mapping location on SMG

- Question 18: Mapping SKU
- Answer 18: 
  + Ask logistic team send product name and SKU
  + Get product id from product name on TMS (Product Management)
  + Run below SQL
    ```sql
    INSERT INTO pd_product_mapping(prod_mapping_id, product_id, product_code, product_name, partner_id, partner_product_id, partner_product_code, partner_product_name, status, modifyby, modifydate, org_id) 
    VALUES ((select MAX(prod_mapping_id) + 1 from pd_product_mapping), {product_id}, NULL,'{product_name}', {partner_id}, {partner_product_id}, '{sku}', NULL, 1, NULL, now(),{org_id});
    ```

- Question 19: DO status in TMS does not update
- Answer 19: Restart 05_ffm-callback

- Question 20: Change status DO by user request
- Answer 20: run below curl for updating
  ```sh
  curl --location --request PUT '{TMS_domain}/api/v1/support/status-do' \
    --header 'Content-Type: application/json' \
    --header 'Authorization: Bearer {token}' \
    --data '[
    {
        "soId": "447055,447054", // list SO ID
        "status": 59, // Status need to update
        "requestBy": "Quách Ngọc Thảo", // User request
        "requestTime": "2024-01-09 17:50" // Time need to update
    } 
    ]'
  ```

- Question 21: MKT request change source name
- Answer 21: run below curl for updating
  ```sh
  curl --location --request POST '{smg_api_domain}/api/v1/support/change-source-name?oldName={old_code}&newName={new_name}\
  --header 'Authorization: Bearer {token}'
  ```

- Question 22: Restart the app when the offer is created.
- Answer 22: Restart 01_api, 01_batchjob

- Question 23: Postback status lead missed
- Answer 23: run below curl for updating
  ```sh
  "curl --request PUT \
    --url 'https://naturalorigin.scaletrk.com/api/v2/network/leads/update?api-key=4e0fb2de8ff75ef02b933aeff4cbcb2f4ece91e0' \
    --header 'content-type: application/json' \
    --data '{
    "lead_id": "{{click_id}}",
    "conv_status": "trash", // approve, reject
    "adv_user_id": "{{lead_id}}",
    "adv_param1": "{{reason}}",
    "ip": "",
    "adv_param4": ""
  }'"
  ```

## SMG system

- Question 1: User cannot login to SMG
- Answer 1: Go to TMS system -> User -> Users -> Select user & change password

- Question 2: SMG loads slowly
- Answer 2: check case by case:
  + Restart SMG API
  + Check workload of database (go to Grafana monitor database)

- Question 3: Logistic cannot map location on SMG
- Answer 3: check case by case:
  + Check format file of user must be same with TMS
  + Check with column is required, it's not null or blank
  + Remove Excel format before upload on SMG

## Rescue Management System (RMS)

- Question 1: Rescue loads slowly
- Answer 1: 
  + Restart 08_rescue_api
  + Check workload of database (go to Grafana monitor database)

- Question 2: User inform missing rescue cases
- Answer 2: run below curl for updating
  ```sh
  curl --request POST \
  --url https://{TMS_domain}/api/v1/callback/trigger-rescues \
  --header 'authorization: Bearer {token}' \
  ````

