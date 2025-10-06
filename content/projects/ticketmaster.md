---
title: "Ticketmaster Database Project"
---

## Ticketmaster Database Project  

## **September - November 2024**

**Tech stack:** MySQL, ER Diagrams, Database Normalisation  

---

## Overview  

From September to November 2024, I collaborated with a team of eight to design a fully normalized **database structure inspired by Ticketmaster**. The goal was to understand the complexity of real-world systems — managing entities like users, events, venues, and payments — and model them efficiently through normalization and relationship mapping.  

This project began as a **group collaboration** and transitioned into an **independent phase**, where each member refined and implemented the shared design based on personal analysis and justifications.  

---

## Teamwork & Communication  

Working in a large team required consistent communication, coordination, and compromise. I played a key role in facilitating discussions, ensuring all voices were heard during the **entity–attribute discovery phase**, and leading consensus-building on design decisions such as key selection and relationship mapping.  

We adopted a **collaborative workflow**:  

- Shared ER diagrams and schemas through a discovery phase of the Ticketmaster website.
- Held weekly team reviews to align design interpretations and important entity relationship discoveries.  
- Combined critical analysis with open communication to finalise our entity relationships.  

---

## Independent Phase  

After the group stage, the project evolved into an individual piece of work. I focused on **normalising the schema** and ensuring efficiency in table relationships — particularly the complex **many-to-many** structures between tickets, events, and venues.  

The final report justified every deviation from the original group model, reflecting both **independent problem-solving** and **analytical reasoning** in refining data structures.  

---

## Final Schema  

{{< figure src="/assets/ticketmasterERD.png" alt="Ticketmaster ER Diagram" width="1500px" >}}  

---

## Code Snippet  

```sql
-- Process of creating a user and simulating them buying a ticket

START TRANSACTION;
 -- creating a user
INSERT INTO user_phone_number (phone_number_id, dialling_code_id, user_phone_number)
VALUES (NULL, 1, 7704720396);

SET @user_phone_number_id = LAST_INSERT_ID();

SET @password := 'password';
SET @random_number := RAND();
SET @hashed_random_number := SHA1(@random_number);
SELECT @salt := SUBSTRING(@hashed_random_number, 1, 16); 
SELECT @salt_password := CONCAT(@salt, @password);
SELECT @salted_hash := SHA1(@salt_password);
SELECT @stored_salted_hash := CONCAT(@salt, @salted_hash);

INSERT INTO users (first_name, last_name, email, u_phone_number_id, password)
VALUES ('Josh', 'Culbert', 'jculbert@email.com', @user_phone_number_id, @stored_salted_hash);
SET @user_id = LAST_INSERT_ID();

INSERT INTO customer_address (c_address_line_1, c_address_line_2, c_address_line_3, house_name, house_number, postcode_id, country_id)
VALUES ('11 Newtown Grange', 'Newtownards', NULL, NULL, 11, 1, 1);

SET @customer_address_id = LAST_INSERT_ID();

-- creating a customer to begin an order
INSERT INTO customer (user_id, customer_address_id)
VALUES (@user_id, @customer_address_id);
SET @customer_id = LAST_INSERT_ID();

-- get event info 
SELECT event_lineup.event_id INTO @event
FROM event_lineup
INNER JOIN performer ON performer.performer_id = event_lineup.performer_id
INNER JOIN event_info ON event_info.event_id = event_lineup.event_id
INNER JOIN venue ON venue.venue_id = event_info.venue_id
WHERE performer.performer_name = "Taylor Swift"
  AND venue.venue_name = "OVO Hydro";


-- get seat info
SELECT seat.seat_id INTO @seat1
FROM seat
INNER JOIN seat_type on seat_type.seat_id = seat.seat_id
INNER JOIN ticket ON ticket.seat_id = seat.seat_id
INNER JOIN ticket_type ON ticket_type.ticket_type_id = ticket.ticket_type_id
WHERE ticket_type.wheelchair_access = 1
    AND seat_type.accessiblity =1
  AND seat.is_seat_booked = 0
LIMIT 1;

UPDATE seat
SET is_seat_booked = 1
WHERE seat_id = @seat1;

SELECT seat.seat_id INTO @seat2
FROM seat
INNER JOIN seat_type on seat_type.seat_id = seat.seat_id
INNER JOIN ticket ON ticket.seat_id = seat.seat_id
INNER JOIN ticket_type ON ticket_type.ticket_type_id = ticket.ticket_type_id
WHERE ticket_type.wheelchair_access = 1
    AND seat_type.accessiblity =1
  AND seat.is_seat_booked = 0
LIMIT 1;

UPDATE seat
SET is_seat_booked = 1
WHERE seat_id = @seat2;

SELECT ticket_pricing.base_price INTO @ticket_price
FROM ticket_pricing
INNER JOIN event_lineup ON event_lineup.event_lineup_id = ticket_pricing.event_lineup_id
INNER JOIN performer ON performer.performer_id = event_lineup.performer_id
INNER JOIN event_info ON event_info.event_id = event_lineup.event_id
INNER JOIN venue ON venue.venue_id = event_info.venue_id
INNER JOIN ticket_type ON ticket_type.ticket_type_id = ticket_pricing.ticket_type_id
WHERE performer.performer_name = "Taylor Swift"
  AND venue.venue_name = "OVO Hydro"
  AND ticket_type.wheelchair_access = 1;

SELECT ticket_pricing.ticket_pricing_id INTO @ticket_price_id
FROM ticket_pricing
INNER JOIN event_lineup ON event_lineup.event_lineup_id = ticket_pricing.event_lineup_id
INNER JOIN performer ON performer.performer_id = event_lineup.performer_id
INNER JOIN event_info ON event_info.event_id = event_lineup.event_id
INNER JOIN venue ON venue.venue_id = event_info.venue_id
INNER JOIN ticket_type ON ticket_type.ticket_type_id = ticket_pricing.ticket_type_id
WHERE performer.performer_name = "Taylor Swift"
  AND venue.venue_name = "OVO Hydro"
  AND ticket_type.wheelchair_access = 1;

SELECT delivery_method.delivery_method_id into @delivery_id
FROM delivery_method
WHERE delivery_method.delivery_type = 'Delivery';

SELECT delivery_method.delivery_price into @delivery_price
FROM delivery_method
WHERE delivery_method.delivery_type = 'Delivery';

INSERT INTO orders (quantity, order_date, total_price, order_status, customer_id, ticket_pricing_id, delivery_method_id)
VALUES (2, NOW(), ((2 * @ticket_price) + @delivery_price), 0, @customer_id, @ticket_price_id, @delivery_id);
SET @order_id = LAST_INSERT_ID();

SELECT total_price INTO @total_amount
FROM orders
WHERE order_id = @order_id;

SELECT payment_type.payment_type_id INTO @payment_type
FROM payment_type
WHERE payment_type.payment_type = 'Card';

INSERT INTO user_payment_info (payment_type_id, user_id)
VALUES (@payment_type, @user_id);
SET @user_payment_id = LAST_INSERT_ID();

SET @card_number := 4060104020395467;
SELECT @salt_card_number := CONCAT(@salt, @card_number);
SELECT @salted_hash := SHA1(@salt_card_number);
SELECT @stored_salted_hash := CONCAT(@salt, @salted_hash);

INSERT INTO card_details (card_number, expiry_date, payment_type_id)
VALUES (@stored_salted_hash, '2030-11-01', @payment_type);
SET @card_details = LAST_INSERT_ID();

INSERT INTO payment (payment_type_id, conditions_met, payment_amount, payment_date, order_id, currency_id)
VALUES (@payment_type, 1, @total_amount, NOW(), @order_id, 1);

UPDATE orders
SET orders.order_status =1
WHERE orders.order_id = @order_id;


COMMIT;

SELECT 
    o.order_id, 
    o.quantity, 
    o.order_date, 
    o.total_price,
    CASE 
        WHEN o.order_status = 1 THEN 'Order Completed'
        WHEN o.order_status = 0 THEN 'Order Failed'
        ELSE 'Unknown'
    END AS order_status,
    u.first_name, 
    u.last_name,
    tp.base_price,
    dm.delivery_price
FROM 
    orders o
INNER JOIN 
    customer c ON o.customer_id = c.customer_id
INNER JOIN 
    users u ON c.user_id = u.user_id
INNER JOIN 
    ticket_pricing tp ON o.ticket_pricing_id = tp.ticket_pricing_id
INNER JOIN 
    delivery_method dm ON o.delivery_method_id = dm.delivery_method_id
WHERE 
    o.order_id = @order_id
    AND c.customer_id = @customer_id
    AND tp.ticket_pricing_id = @ticket_price_id
    AND dm.delivery_method_id = @delivery_id;

