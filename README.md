41. CREATE TABLE investors (
    id INT PRIMARY KEY,
    email VARCHAR(255) UNIQUE
);

CREATE TABLE cash_flows (
    investor_id INT,
    expected_flow DECIMAL(8,2),
    FOREIGN KEY (investor_id) REFERENCES investors(id)
);

INSERT INTO investors (id, email) VALUES
(1, 'tdowner0@timesonline.co.uk'),
(2, 'cgarza1@opera.com'),
(3, 'nbarwise2@si.edu');


INSERT INTO cash_flows (investor_id, expected_flow) VALUES
(1, 24923.83),
(1, 30212.10),
(1, 87126.50),
(1, 56018.65),
(1, 93357.47),
(1, 55073.54),
(1, 27095.07),
(2, 44165.12),
(2, 43658.84),
(2, 35835.34),
(2, 12660.46),
(2, 58676.60),
(2, 95929.25),
(2, 47161.23),
(2, 80283.91),
(2, 54427.20),
(2, 93223.98),
(3, 19741.35),
(3, 171261.97),
(3, 86463.11),
(3, 56931.73),
(3, 194699.58);

SELECT 
	i.email, 
	COUNT(c.investor_id) AS investment_count, 
	SUM(c.expected_flow) AS total_expected_flow, 
	MAX(c.expected_flow) - MIN(c.expected_flow) AS range_expected_flow 
FROM 
	investors i 
INNER JOIN 
	cash_flows c ON i.id = c.investor_id 
GROUP By
	i.id
HAVING
	SUM(c.expected_flow) > 100000
ORDER BY
	i.email;


42. 

CREATE TABLE coupons (
    id INT PRIMARY KEY,
    coupon_code VARCHAR(255) UNIQUE,
    description VARCHAR(255),
    is_enabled SMALLINT
);

CREATE TABLE coupon_uses (
    coupon_id INT,
    amount DECIMAL(4,2),
    FOREIGN KEY (coupon_id) REFERENCES coupons(id)
);


INSERT INTO coupons (id, coupon_code, description, is_enabled) VALUES
(1, 'COUPON123', 'nisi nam ultrices libero non', 0),
(2, 'SAVE20', 'ac est lacinia', 1),
(3, 'DISCOUNT50', 'quis odio consequat', 1);


INSERT INTO coupon_uses (coupon_id, amount) VALUES
(1, 36.68),
(1, 3.56),
(1, 2.10),
(1, 39.58),
(2, 39.81),
(2, 24.07),
(2, 28.42),
(2, 31.03),
(2, 3.24),
(2, 36.33),
(3, 8.89),
(3, 30.44),
(3, 36.94),
(3, 42.65),
(3, 33.61),
(3, 41.92),
(3, 1.78),
(3, 20.26),
(3, 27.92),
(3,0.23);

SELECT c.coupon_code,
c.description, 
COUNT(cu.coupon_id) AS total_uses,
MIN(cu.amount) AS min_discount,
MAX(cu.amount) AS max_discount,
ROUND(AVG(cu.amount), 2) AS avg_discount
FROM coupons c
JOIN coupon_uses cu ON c.id = cu.coupon_id
WHERE 
c.is_enabled = 1
GROUP BY
c.coupon_code,
c.description
ORDER BY
c.coupon_code ASC;


43. CREATE TABLE professions (
    id INT PRIMARY KEY,
    title VARCHAR(255)
);

CREATE TABLE freelancers (
    id INT PRIMARY KEY,
    profession_id INT,
    email VARCHAR(255),
    FOREIGN KEY (profession_id) REFERENCES professions(id)
);

CREATE TYPE project_status AS ENUM ('Completed', 'Ongoing', 'Cancelled');
CREATE TABLE projects (
    id INT PRIMARY KEY,
    freelancer_id INT,
    status PROJECT_STATUS,
    income DECIMAL(6,2),
    FOREIGN KEY (freelancer_id) REFERENCES freelancers(id)
);


INSERT INTO professions (id, title) VALUES
(1, 'Artificial Intelligence Engineer'),
(2, 'Network Administrator'),
(3, 'Game Developer');

INSERT INTO freelancers (id, profession_id, email) VALUES
(1, 1, 'lfernez0@microsoft.com'),
(3, 2, 'mbrydone2@delicious.com'),
(4, 2, 'jhamp3@4shared.com'),
(5, 3, 'cparfett4@twitter.com');

INSERT INTO projects (id, freelancer_id, status, income) VALUES
(5, 1, 'Completed', 8562.13),
(11, 1, 'Completed', 6727.56),
(10, 3, 'Completed', 3753.46),
(20, 3, 'Completed', 6659.39),
(6, 4, 'Completed', 8459.28),
(13, 4, 'Completed', 5899.31),
(16, 4, 'Completed', 2709.63),
(4, 5, 'Completed', 5029.44),
(7, 5, 'Completed', 1763.94),
(9, 5, 'Completed', 6988.36),
(8, 3, 'Cancelled', 8699.67),
(1, 5, 'Cancelled', 5403.21),
(19, 3, 'Ongoing', 72.51),
(3, 4, 'Ongoing', 8561.14),
(15, 4, 'Ongoing', 9235.78),
(17, 4, 'Ongoing', 4307.76);

SELECT
    p.title, 
    COUNT(CASE WHEN pj.status = 'Completed' THEN pj.id END) AS total_projects,
    SUM(CASE WHEN pj.status = 'Completed' THEN pj.income END) AS total_income,
    COUNT(DISTINCT f.id) AS total_freelancers,
    ROUND(SUM(CASE WHEN pj.status = 'Completed' THEN pj.income END) / COUNT(DISTINCT f.id), 2) AS average_income_per_freelancer
FROM 
    professions p
LEFT JOIN
    freelancers f ON p.id = f.profession_id
LEFT JOIN
    projects pj ON f.id = pj.freelancer_id
GROUP BY
    p.id
ORDER BY
    total_income DESC;


44. CREATE TABLE categories (
    id INT PRIMARY KEY,
    title VARCHAR(255) UNIQUE
);

CREATE TABLE products (
    id INT PRIMARY KEY,
    category_id INT,
    title VARCHAR(255),
    sku VARCHAR(255) UNIQUE,
    stock_number INT,
    FOREIGN KEY (category_id) REFERENCES categories(id)
);

INSERT INTO categories (id, title) VALUES
(1, 'Electronics'),
(2, 'Clothing'),
(3, 'Home & Kitchen');


INSERT INTO products (id, category_id, title, sku, stock_number) VALUES
(11, 1, 'Elegant Gadget', 'EG-11', 4),
(3, 1, 'Luxury Gizmo', 'LG-3', 10),
(19, 1, 'Sleek Widget', 'SW-19', 8),
(8, 1, 'Sleek Widget', 'SW-8', 8),
(14, 2, 'Elegant Gadget', 'EG-14', 2),
(16, 2, 'Elegant Gadget', 'EG-16', 6),
(10, 2, 'Elegant Gadget', 'EG-10', 10),
(7, 2, 'Luxury Gizmo', 'LG-7', 3),
(2, 2, 'Luxury Gizmo', 'LG-2', 8),
(18, 2, 'Luxury Gizmo', 'LG-18', 9),
(1, 2, 'Sleek Widget', 'SW-1', 3),
(6, 2, 'Sleek Widget', 'SW-6', 7),
(20, 3, 'Elegant Gadget', 'EG-20', 10),
(9, 3, 'Luxury Gizmo', 'LG-9', 4),
(12, 3, 'Luxury Gizmo', 'LG-12', 5),
(13, 3, 'Luxury Gizmo', 'LG-13', 5),
(5, 3, 'Luxury Gizmo', 'LG-5', 9),
(4, 3, 'Sleek Widget', 'SW-4', 8),
(15, 3, 'Sleek Widget', 'SW-15', 9),
(17, 3, 'Sleek Widget', 'SW-17', 9);

SELECT c.title AS category,
p.title AS title,
SUM(p.stock_number) AS total_stock
FROM categories c 
JOIN products p ON c.id = p.category_id
GROUP BY
p.title,
c.title
HAVING
SUM(p.stock_number) > 10
ORDER BY
p.title ASC,
c.title ASC,
total_stock DESC;



45. CREATE TABLE threat_types (
    id INT PRIMARY KEY,
    threat_type VARCHAR(255)
);
create type st as ENUM('Quarantined', 'Safe', 'Deleted');

CREATE TABLE quarantine_urls (
    id INT PRIMARY KEY,
    threat_id INT,
    domain_name VARCHAR(255),
    status st,
    users_affected INT,
    FOREIGN KEY (threat_id) REFERENCES threat_types(id)
);

INSERT INTO threat_types (id, threat_type) VALUES
(1, 'Phishing'),
(2, 'Rootkit'),
(3, 'Malware');

INSERT INTO quarantine_urls (id, threat_id, domain_name, status, users_affected) VALUES
(17, 1, 'amazon.com', 'Quarantined', 862),
(16, 1, 'google.com', 'Quarantined', 63),
(9, 1, 'amazon.com', 'Quarantined', 41),
(18, 2, 'amazon.com', 'Quarantined', 149),
(12, 2, 'yahoo.com', 'Quarantined', 967),
(4, 3, 'amazon.com', 'Quarantined', 377),
(10, 3, 'yahoo.com', 'Quarantined', 721),
(11, 1, 'yahoo.com', 'Deleted', 551),
(20, 1, 'amazon.com', 'Safe', 407),
(19, 1, 'amazon.com', 'Deleted', 665);

SELECT q.domain_name AS domain_name,
t.threat_type AS threat_type,
COUNT(q.users_affected) AS total_occurrences,
SUM(q.users_affected) AS total_users_affected
FROM threat_types t
JOIN quarantine_urls q ON t.id = q.threat_id
WHERE
q.status = 'Quarantined'
GROUP BY
q.domain_name,
t.threat_type
ORDER BY
total_users_affected DESC,
domain_name ASC;
 



46. CREATE TABLE clients (
    id INT PRIMARY KEY,
    mac_address VARCHAR(255)
);

CREATE TYPE STREAM AS ENUM ('240p','360p','480p','720p','1080p','1440p','2160p');

CREATE TABLE streams (
    client_id INT,
    title VARCHAR(255),
    quality STREAM,
    traffic INT,
    FOREIGN KEY (client_id) REFERENCES clients(id)
);

INSERT INTO clients (id, mac_address) VALUES
(1, '2F-80-8E-F2-0E-4C'),
(2, 'A1-F7-D4-48-B9-E6'),
(3, '9F-72-DB-7C-73-FC');

INSERT INTO streams (client_id, title, quality, traffic) VALUES
(1, 'Monte Carlo', '360p', 71928308),
(1, 'Separation, The (Sparation, La)', '480p', 35221785),
(1, 'Felidae', '480p', 54617023),
(1, 'Dirty Dancing', '1440p', 56419563),
(1, 'Ragtime', '1440p', 12404457),
(1, 'Oscar', '1440p', 49717246),
(1, 'Barb Wire', '2160p', 83761463),
(1, 'Jason and the Argonauts', '2160p', 27364051),
(2, 'Carry on Cruising', '240p', 33226462),
(2, 'Best of the Best', '240p', 62793858),
(2, 'Ecstasy (xtasis)', '240p', 73079415),
(2, 'Go Go Tales', '480p', 48836837),
(2, 'Nights and Weekends', '1440p', 32708277),
(3, 'Coneheads', '480p', 92308213),
(3, 'Silences of the Palace, The (Saimt el Qusur)', '480p', 52917945),
(3, 'Good Pick', '720p', 71890218),
(3, 'Wuthering Heights', '720p', 19813053),
(3, 'Big Kahuna, The', '1080p', 28786846),
(3, 'Work of Director Michel Gondry, The', '2160p', 18789351),
(3, 'My Best Friends', '2160p', 44347338);

SELECT 
c.mac_address AS mac_address,
COUNT(s.quality) AS streams,
SUM(s.traffic) AS total_traffic
FROM clients c
JOIN streams s ON c.id = s.client_id
WHERE 
s.quality >= '720p'
GROUP BY
c.mac_address
ORDER BY
total_traffic DESC;



47. CREATE TABLE networks (
  id   INT PRIMARY KEY,
  cidr VARCHAR(255)  -- IP Address v4 CIDR
);

CREATE TABLE instances (
  network_id    INT,           -- FK to networks.id
  cpu_usage     VARCHAR(255),  -- CPU usage as shown in sample (e.g., '20%')
  memory_usage  VARCHAR(255),  -- Memory usage (e.g., '74%')
  network_usage VARCHAR(255),  -- Network usage (e.g., '79%')
  CONSTRAINT fk_instances_network
    FOREIGN KEY (network_id) REFERENCES networks(id)
);

INSERT INTO networks (id, cidr) VALUES
(1, '24.77.36.156/9'),
(2, '74.213.138.70/7'),
(3, '167.244.163.58/29');
INSERT INTO instances (network_id, cpu_usage, memory_usage, network_usage) VALUES
(1, '20%', '74%', '74%'),
(3, '26%', '9%',  '99%'),
(3, '2%',  '21%', '97%'),
(1, '51%', '19%', '89%'),
  (2, '2%',  '27%', '79%'),
  (3, '92%', '35%', '41%'),
  (2, '27%', '5%',  '44%'),
  (3, '67%', '47%', '79%'),
  (1, '14%', '28%', '43%'),
  (3, '47%', '0%',  '53%'),
  (1, '38%', '3%',  '46%'),
  (2, '71%', '51%', '6%'),
  (3, '77%', '74%', '53%'),
  (3, '31%', '48%', '80%'),
  (2, '31%', '42%', '24%'),
  (1, '77%', '65%', '46%'),
  (2, '51%', '94%', '41%'),
  (3, '8%',  '3%',  '57%'),
  (1, '1%',  '56%', '62%'),
  (2, '15%', '66%', '65%');

SELECT 
	n.cidr,
COUNT(*) AS instances,
	CONCAT(CEIL(AVG(CAST(REPLACE(i.cpu_usage, '%', '') AS INT))), '%') AS avg_cpu_usage,
	CONCAT(CEIL(AVG(CAST(REPLACE(i.memory_usage, '%', '') AS INT))), '%') AS avg_memory_usage,
	CONCAT(CEIL(AVG(CAST(REPLACE(i.network_usage, '%', '') AS INT))), '%') AS avg_network_usage
FROM 
	networks n
JOIN 
	instances i ON n.id = i.network_id
WHERE
	n.id IN (
	SELECT DISTINCT network_id
	FROM instances
	WHERE CAST(REPLACE(cpu_usage, '%', '') AS INT) >= 80
)
GROUP BY
	n.id,
	n.cidr
ORDER BY
	n.cidr ASC;



48. CREATE TABLE tasks (
    id INT PRIMARY KEY,
    hash VARCHAR(255)
);

CREATE TABLE processes (
    task_id INT,
    start_dt VARCHAR(19),
    end_dt VARCHAR(19),
    FOREIGN KEY (task_id) REFERENCES tasks(id)
);
INSERT INTO tasks (id, hash) VALUES
(1, '208f95e0fcff7926f17ade3cebf33ad9'),
(2, '0f4a49ffead2f18a7f25425c1260fc74'),
(3, 'dbcf54e94935c32e01ec09a5db731912');

INSERT INTO processes (task_id, start_dt, end_dt) VALUES
(1, '2023-04-20 02:01:16', '2023-04-20 02:11:35'),
(1, '2023-04-09 15:11:10', '2023-04-09 15:26:43'),
(1, '2023-04-07 23:41:49', '2023-04-08 00:34:10'),
(2, '2023-04-07 23:05:47', '2023-04-08 00:00:05'),
(2, '2023-04-19 18:39:33', '2023-04-19 18:54:57'),
(2, '2023-04-28 13:17:11', '2023-04-28 13:24:37'),
(2, '2023-04-16 00:13:06', '2023-04-16 01:02:39'),
(2, '2023-04-16 15:02:26', '2023-04-16 15:58:14'),
(2, '2023-04-27 02:23:07', '2023-04-27 02:59:13'),
(2, '2023-04-10 23:33:47', '2023-04-11 00:09:35'),
(2, '2023-04-16 17:29:51', '2023-04-16 18:10:22'),
(2, '2023-04-23 12:16:01', '2023-04-23 12:48:07'),
(3, '2023-04-01 02:25:12', '2023-04-01 02:49:26'),
(3, '2023-04-04 03:02:43', '2023-04-04 03:42:03'),
(3, '2023-04-10 22:42:26', '2023-04-10 23:14:42'),
(3, '2023-04-09 17:46:12', '2023-04-09 18:10:19'),
(3, '2023-04-25 15:09:36', '2023-04-25 15:19:54'),
(3, '2023-04-19 14:39:52', '2023-04-19 15:21:23'),
(3, '2023-04-12 04:22:29', '2023-04-12 04:25:10'),
(3, '2023-04-25 07:40:26', '2023-04-25 08:01:30'); 

SELECT t.hash,
CEIL(SUM(EXTRACT(EPOCH FROM(p.end_dt::TIMESTAMP - p.start_dt::TIMESTAMP)))) AS usage_time
FROM tasks t 
JOIN processes p ON t.id = p.task_id
GROUP BY
t.hash
ORDER BY
t.hash DESC;



49. CREATE TABLE devices (
    id INT PRIMARY KEY,
    score INT
);

INSERT INTO devices (id, score) VALUES
(1, 20),
(2, 50),
(3, 50),
(4, 68),
(5, 95);

SELECT 
CONCAT('Devices ',id,' has ', 'class: ',
CASE
WHEN score >= 80 THEN 'A' 
WHEN score >= 60 AND score < 80 THEN 'B'
WHEN score >= 40 AND score < 60 THEN 'C'
WHEN score >= 20 AND score < 40 THEN 'D'
WHEN score < 20 THEN 'F'
END)
AS device
FROM devices d
ORDER BY
d.id ASC;


50. CREATE TABLE accounts (
    id INT PRIMARY KEY,
    username VARCHAR(50),
    email VARCHAR(100)
);
CREATE TYPE TARIFF AS ENUM('A','B','C','D','E');
CREATE TABLE tariffs (
    id INT PRIMARY KEY,
    name TARIFF,
    cost DECIMAL(4, 3)
);

CREATE TABLE readings (
    account_id INT,
    tariff_id INT,
    amount SMALLINT,
    FOREIGN KEY (account_id) REFERENCES accounts(id),
    FOREIGN KEY (tariff_id) REFERENCES tariffs(id)
);

INSERT INTO accounts (id, username, email) VALUES
(1, 'hshillabeare0', 'rcalkin@sourceforge.net'),
(2, 'sdandy1', 'agaule1@businessweek.com'),
(3, 'sgreiswood2', 'toppy2@lulu.com');

INSERT INTO tariffs (id, name, cost) VALUES
(1, 'A', 0.010),
(2, 'B', 0.020),
(3, 'C', 0.050),
(4, 'D', 0.075),
(5, 'E', 0.100);

INSERT INTO readings (account_id, tariff_id, amount) VALUES
(1, 2, 54),
(1, 3, 19),
(1, 3, 37),
(1, 3, 89),
(1, 3, 119),
(2, 1, 12),
(2, 1, 44),
(2, 1, 81),
(2, 2, 60),
(2, 2, 164),
(2, 2, 199),
(2, 3, 79),
(2, 5, 186),
(3, 1, 31),
(3, 1, 59),
(3, 1, 77),
(3, 1, 95),
(3, 1, 110),
(3, 1, 125),
(3, 2, 31);

SELECT 
a.username,
a.email,
MAX(t.name) AS highest_tariff,
SUM(r.amount) AS consumption,
ROUND(SUM(t.cost * r.amount), 2) AS total_cost
FROM 
accounts a
JOIN 
readings r ON a.id = r.account_id
JOIN 
tariffs t ON t.id = r.tariff_id
GROUP BY
a.username,
a.email
ORDER BY
a.username ASC;




51. CREATE TABLE cities (
    id INT PRIMARY KEY,
    name VARCHAR(100)
);

CREATE TABLE banners (
    city_id INT,
    width INT,
    height INT,
    FOREIGN KEY (city_id) REFERENCES cities(id)
);

INSERT INTO cities (id, name) VALUES
(1, 'Kayu Agung'),
(2, 'Yangkou'),
(3, 'Marseille');

INSERT INTO banners (city_id, width, height) VALUES
(3, 6, 20),
(1, 20, 14),
(1, 6, 17),
(1, 15, 6),
(2, 16, 8),
(2, 6, 7),
(3, 6, 9),
(1, 20, 16),
(3, 19, 14),
(2, 9, 17);

INSERT INTO banners (city_id, width, height) VALUES
(2, 8, 12),
(1, 12, 16),
(3, 15, 14),
(3, 11, 7),
(3, 6, 14),
(2, 12, 7),
(3, 7, 20),
(1, 13, 6),
(3, 10, 13),
(2, 19, 15);


SELECT
c.name,
COUNT(b.city_id) AS banners,
MIN(b.height * b.width) AS min_area,
ROUND(AVG(b.height * b.width), 2) AS avg_area,
MAX(b.height * b.width) AS max_area,
SUM(b.height * b.width) AS total_area
FROM cities c 
JOIN banners b ON c.id = b.city_id
GROUP BY
c.name
ORDER BY
c.name ASC;



53. CREATE TABLE lots (
    id INT PRIMARY KEY,
    name VARCHAR(255)
);

CREATE TABLE offers (
    lot_id INT,
    amount DECIMAL(6,2),
    FOREIGN KEY (lot_id) REFERENCES lots(id)
);
INSERT INTO lots (id, name) VALUES
(1, 'Merremia quinquefolia (L.) Hallier f.'),
(2, 'Plantago maritima L.'),
(3, 'Hohenbergia antillana Mez'),
(4, 'Penstemon eriantherus Pursh var. argillosus M.E. Jones');

INSERT INTO offers (lot_id, amount) VALUES
(1, 510.51), (2, 703.80), (2, 181.80), (1, 38.06), (2, 368.78),
(3, 91.40), (2, 413.80), (3, 157.99), (3, 885.82), (2, 863.99),
(1, 307.61), (2, 120.39), (1, 771.96), (2, 801.42), (3, 871.59),
(1, 541.61), (3, 477.62), (2, 303.29), (2, 612.83), (3, 464.98);


SELECT 
l.name,
COUNT(o.lot_id) AS offers,
ROUND(MIN(o.amount),2) AS min_offer,
ROUND(AVG(o.amount),2) AS avg_offer,
ROUND(MAX(o.amount),2) AS max_offer
FROM 
lots l
LEFT JOIN 
offers o ON l.id = o.lot_id
GROUP BY
l.name
ORDER BY
offers DESC;



54. -- DROP TABLE accounts CASCADE;
-- DROP TABLE declarations CASCADE;
CREATE TABLE accounts (
    id INT PRIMARY KEY,
    first_name VARCHAR(255),
    last_name VARCHAR(255),
    iban VARCHAR(255)
);

CREATE TYPE quarter_enum AS ENUM ('Q1', 'Q2', 'Q3', 'Q4');

CREATE TABLE declarations (
    account_id INT,
    quarter quarter_enum,
    income DECIMAL(7,2),
    FOREIGN KEY (account_id) REFERENCES accounts(id)
);

INSERT INTO accounts (id, first_name, last_name, iban) VALUES
(1, 'Alex', 'Cantua', 'IL29 9590 1551 0560 0553 712'),
(2, 'Chris', 'Lashmore', 'AZ54 CNUI 01DR XEXZ ASKY QM4W F8JI'),
(3, 'Taylor', 'Blum', 'HR20 2041 7741 5014 9873 9'),
(4, 'Robin', 'Neachell', 'NL87 PPCD 0429 1849 92'),
(5, 'Drew', 'Barbier', 'FR72 7843 3990 42WM QC8P GVNV F78');

INSERT INTO declarations (account_id, quarter, income) VALUES
(1, 'Q1', 49235.67), (1, 'Q2', 46653.11), (1, 'Q3', 63739.99), (1, 'Q4', 43222.54),
(2, 'Q1', 69743.50), (2, 'Q2', 29641.01), (2, 'Q3', 97725.49), (2, 'Q4', 91481.98),
(3, 'Q1', 68402.43), (3, 'Q2', 12660.12), (3, 'Q3', 59601.65), (3, 'Q4', 54701.74),
(4, 'Q1', 55220.27), (4, 'Q2', 87752.41), (4, 'Q3', 44447.06), (4, 'Q4', 45876.26),
(5, 'Q1', 42511.74), (5, 'Q2', 22022.78), (5, 'Q3', 88396.81), (5, 'Q4', 67252.54);

SELECT 
CONCAT(last_name,'',first_name) AS full_name,
a.iban,
SUM(d.income) AS income,
'10%' AS rate,
ROUND(SUM(income * 10), 2) AS tax
FROM 
accounts a
JOIN 
declarations d ON a.id = d.account_id
GROUP BY
a.iban,
CONCAT(last_name,'',first_name) 
ORDER BY
full_name ASC;



55. CREATE TABLE profiles (
    id INT PRIMARY KEY,
    first_name VARCHAR(255),
    last_name VARCHAR(255),
    email VARCHAR(255)
);

CREATE TABLE relations (
    profile_id INT,
    related_to VARCHAR(255),
    is_approved BOOLEAN,
    FOREIGN KEY (profile_id) REFERENCES profiles(id)
);
INSERT INTO profiles (id, first_name, last_name, email) VALUES
(1, 'Shayne', 'Shilito', 'sshilito0@ftc.gov'),
(2, 'Shell', 'Shade', 'sshade1@paginegialle.it'),
(3, 'Nobie', 'Splain', 'nsplain2@npr.org');

INSERT INTO relations (profile_id, related_to, is_approved) VALUES
(1, 'cbasinigazzii', TRUE), (1, 'ldevered', TRUE), (1, 'edeniskeb', TRUE),
(1, 'cstirland4', TRUE), (1, 'ngooddiea', TRUE), (1, 'alockney7', TRUE),
(1, 'jsorrillj', FALSE), (1, 'bnodin3', FALSE), (1, 'dwall2', FALSE),
(1, 'folivas1', FALSE), (2, 'ksharland6', FALSE), (2, 'pbarosch8', FALSE),
(2, 'smacieja9', FALSE), (2, 'bbrasonf', FALSE), (2, 'dabrahartg', FALSE),
(3, 'gaymer5', TRUE), (3, 'rwoolcockse', TRUE), (3, 'egilyott0', TRUE),
(3, 'agillionc', FALSE), (3, 'fgribbinh', FALSE);

SELECT 
CONCAT(last_name,'',first_name) AS full_name,
p.email,
COUNT(related_to) AS total_relations,
SUM(CASE WHEN r.is_approved = TRUE THEN 1 ELSE 0 END) AS approved_relations,
SUM(CASE WHEN r.is_approved = FALSE THEN 1 ELSE 0 END) AS pending_relations
FROM profiles p
JOIN relations r ON p.id = r.profile_id
GROUP BY
CONCAT(last_name,'',first_name),
p.email
ORDER BY
full_name ASC;


56. CREATE TABLE accounts (
    id INT PRIMARY KEY,
    iban VARCHAR(255)
);

CREATE TABLE transactions (
    account_id INT,
    dt TIMESTAMP,
    amount DECIMAL(5,2),
    FOREIGN KEY (account_id) REFERENCES accounts(id)
);

INSERT INTO accounts (id, iban) VALUES
(1, 'SE48 2961 2087 8112 2835 6438'),
(2, 'BE89 2286 5514 4847'),
(3, 'MU84 HRGV 2047 2584 5774 3195 856J PZ');

INSERT INTO transactions (account_id, dt, amount) VALUES
(2, '2022-09-25 19:24:50', 75.06),
(2, '2022-09-24 03:09:17', 41.10),
(1, '2022-09-19 04:13:17', 65.85),
(3, '2022-09-30 07:18:29', 44.57),
(1, '2022-09-26 01:51:44', 98.93),
(1, '2022-08-28 02:51:04', 60.42),
(1, '2022-08-25 23:25:54', 45.34),
(2, '2022-09-09 11:00:48', 11.05),
(3, '2022-08-25 19:37:02', 53.61),
(2, '2022-09-23 09:44:05', 89.18),
(1, '2022-08-28 19:48:40', 47.60),
(3, '2022-09-12 10:28:10', 96.40),
(3, '2022-10-03 16:49:51', 45.41),
(2, '2022-09-05 16:20:41', 46.78),
(3, '2022-10-03 04:51:29', 50.81),
(1, '2022-09-10 17:31:44', 78.72),
(2, '2022-08-31 21:59:56', 61.09),
(2, '2022-09-14 12:52:13', 20.36),
(1, '2022-09-28 11:05:21', 70.52),
(3, '2022-09-30 09:21:12', 48.00);

SELECT 
a.iban,
COUNT(t.account_id) AS transactions,
SUM(t.amount) AS total
FROM accounts a
JOIN transactions t ON a.id = t.account_id
WHERE
EXTRACT(MONTH FROM t.dt) = 9 
GROUP BY
a.iban
ORDER BY
total ASC;



57.CREATE TABLE clients (
    id SMALLINT PRIMARY KEY,
    mac VARCHAR(17),
    tariff DECIMAL(6,5)
);

CREATE TABLE traffic (
    client_id SMALLINT,
    dt VARCHAR(19),
    amount INT,
    FOREIGN KEY (client_id) REFERENCES clients(id)
);
INSERT INTO clients (id, mac, tariff) VALUES
(1, 'A2-53-FC-0C-3E-B4', 0.00007),
(2, 'DC-80-42-E9-AE-FC', 0.00003),
(3, '3F-9B-A9-2A-B1-7B', 0.00007),
(4, 'D4-6F-E4-AF-47-D5', 0.00004),
(5, 'B9-65-FC-8E-F0-15', 0.00007);

INSERT INTO traffic (client_id, dt, amount) VALUES
(1, '2022-05-22', 9127),
(1, '2022-06-07', 62203),
(1, '2022-06-10', 88227),
(2, '2022-05-31', 99874),
(2, '2022-06-01', 78400),
(2, '2022-06-03', 61106),
(2, '2022-06-12', 20963),
(2, '2022-06-29', 98304),
(2, '2022-07-04', 6626),
(3, '2022-05-22', 8386),
(3, '2022-06-08', 22959),
(3, '2022-07-05', 52096),
(3, '2022-07-14', 70777),
(4, '2022-05-22', 93743),
(5, '2022-05-16', 84660),
(5, '2022-05-28', 63267),
(5, '2022-06-10', 80681),
(5, '2022-06-21', 55460),
(5, '2022-07-04', 91365),
(5, '2022-07-09', 23296);

SELECT
c.mac,
SUM(t.amount) AS traffic,
ROUND(SUM(t.amount * c.tariff), 2) AS cost
FROM 
clients c
JOIN 
traffic t ON c.id = t.client_id
WHERE 
t.dt LIKE '2022-05%'
GROUP BY
c.mac
ORDER BY
cost DESC;


58.  CREATE TABLE companies (
    id SMALLINT PRIMARY KEY,
    name VARCHAR(255),
    address VARCHAR(255),
    phone VARCHAR(255)
);

CREATE TABLE categories (
    company_id SMALLINT,
    name VARCHAR(255),
    review_rating SMALLINT,
    FOREIGN KEY (company_id) REFERENCES companies(id)
);

INSERT INTO companies (id, name, address, phone) VALUES
(1, 'Casper, Oberbrunner and Williamson', '53 Di Loreto Hill', '+420 (569) 566-3689'),
(2, 'Tromp, Kozey and Abbott', '84 Mcguire Plaza', '+62 (145) 722-2330'),
(3, 'Gerlach, Hayes and Stamm', '80 Service Point', '+86 (731) 234-4119'),
(4, 'Wol -Fadel', '06 Fair Oaks Trail', '+7 (894) 233-0976'),
(5, 'Kihn-Cronin', '483 Nobel Road', '+1 (396) 693-1661');

INSERT INTO categories (company_id, name, review_rating) VALUES
(1, 'HVAC', 2),
(2, 'HVAC', 2), (2, 'Retaining Wall and Brick Pavers', 1), (2, 'Rebar & Wire Mesh Install', 2),
(3, 'Prefabricated Aluminum Metal Canopies', 2), (3, 'Prefabricated Aluminum Metal Canopies', 0),
(3, 'RF Shielding', 2), (3, 'Overhead Doors', 0), (3, 'Rebar & Wire Mesh Install', 5),
(3, 'Termite Control', 0),
(4, 'Sitework & Site Utilities', 0), (4, 'Electrical and Fire Alarm', 2),
(4, 'Masonry', 2), (4, 'Temp Fencing, Decorative Fencing and Gates', 0),
(4, 'Elevator', 1), (4, 'Drywall & Acoustical (FED)', 5),
(5, 'Asphalt Paving', 0), (5, 'Glass & Glazing', 1),
(5, 'Framing (Steel)', 3), (5, 'Structural & Misc Steel Erection', 1);

SELECT c.name,
c.name, 
	c.address, 
	c.phone, 
	CONCAT(ROUND(AVG(ca.review_rating),1),' (', COUNT(c.id), ' categories', ')') AS overall_review_rating
FROM
companies c
JOIN 
categories ca ON c.id = ca.company_id
GROUP BY
c.id
ORDER BY
AVG(ca.review_rating) DESC,
c.name ASC;




59. CREATE TABLE accounts (
    id SMALLINT PRIMARY KEY,
    username VARCHAR(255),
    is_active SMALLINT
);

CREATE TABLE domains (
    account_id SMALLINT,
    name VARCHAR(255),
    expiration_date VARCHAR(19),
    FOREIGN KEY (account_id) REFERENCES accounts(id)
);

INSERT INTO accounts (id, username, is_active) VALUES
(1, 'obeedie0', 0),
(2, 'stopham1', 1),
(3, 'ndolder2', 1),
(4, 'jyanshinov3', 1),
(5, 'ewilinger4', 0);


INSERT INTO domains (account_id, name, expiration_date) VALUES
(1, 'imgur.com', '2022-05-14'),
(1, 'domainmarket.com', '2022-07-02'),
(1, 'comsenz.com', '2022-07-28'),
(1, 'gizmodo.com', '2022-08-09'),
(1, 'toplist.cz', '2022-08-15'),
(1, 'scientificamerican.com', '2022-09-03'),
(1, 'examiner.com', '2022-12-18'),
(1, 'photobucket.com', '2023-01-22'),
(2, 'merriam-webster.com', '2022-02-20'),
(2, 'tripod.com', '2022-08-08'),
(3, 'ca.gov', '2022-04-24'),
(3, 'ehow.com', '2022-06-28'),
(3, 'purevolume.com', '2022-07-01'),
(3, 'squidoo.com', '2022-10-27'),
(3, 'eepurl.com', '2022-12-21'),
(4, 'digg.com', '2022-05-14'),
(4, 'jugem.jp', '2022-08-05'),
(4, 'artisteer.com', '2022-10-21'),
(5, 'behance.net', '2022-03-24'),
(5, 'cnn.com', '2022-05-11');

SELECT 
a.username,
COUNT(d.account_id) AS domains,
MIN(d.expiration_date) AS nearest_expiration
FROM 
accounts a 
JOIN 
domains d ON a.id = d.account_id
WHERE
a.is_active = 1 AND d.expiration_date > '2022-07-15'
GROUP BY
a.username
ORDER BY
a.username ASC;


60. CREATE TABLE campaigns (
    id SMALLINT PRIMARY KEY,
    name VARCHAR(255)
);

CREATE TABLE events (
    campaign_id SMALLINT,
    dt VARCHAR(19),
    value DECIMAL(6,5),
    FOREIGN KEY (campaign_id) REFERENCES campaigns(id)
);


INSERT INTO campaigns (id, name) VALUES
(1, '11-080 - Registration Equipment'),
(2, '12-700 - Systems Furniture'),
(3, '9-900 - Paints and Coatings');

INSERT INTO events (campaign_id, dt, value) VALUES
(1, '2022-07-14 13:11:38', 0.59275), (1, '2022-07-14 14:55:43', 0.12928),
(1, '2022-07-14 18:16:11', 0.82350), (1, '2022-07-15 01:19:44', 0.97144),
(1, '2022-07-15 22:52:02', 0.60728), (1, '2022-07-16 08:55:38', 0.71158),
(1, '2022-07-16 10:22:44', 0.29627),
(2, '2022-07-14 02:36:31', 0.42323), (2, '2022-07-14 04:45:32', 0.91077),
(2, '2022-07-14 07:24:11', 0.35956), (2, '2022-07-15 06:43:08', 0.16662),
(2, '2022-07-16 08:21:27', 0.02559), (2, '2022-07-16 11:59:41', 0.34606),
(2, '2022-07-16 23:26:12', 0.62697),
(3, '2022-07-14 00:21:56', 0.97297), (3, '2022-07-14 10:22:11', 0.93894),
(3, '2022-07-14 12:29:59', 0.44633), (3, '2022-07-15 01:17:41', 0.37531),
(3, '2022-07-15 14:20:48', 0.24872), (3, '2022-07-16 23:02:51', 0.80594);

SELECT
c.name,
COUNT(e.campaign_id),
ROUND(AVG(e.value), 5) AS average_value
FROM campaigns c
JOIN events e ON c.id = e.campaign_id
WHERE
e.dt LIKE '2022-07-15%'
GROUP BY
c.name
HAVING
AVG(e.value) >= 0.7
ORDER BY
average_value DESC;


61. CREATE TABLE profiles (
    id SMALLINT PRIMARY KEY,
    first_name VARCHAR(255),
    last_name VARCHAR(255),
    email VARCHAR(255)
);

CREATE TABLE deals (
    profile_id SMALLINT,
    dt VARCHAR(19),
    amount DECIMAL(5,2),
    FOREIGN KEY (profile_id) REFERENCES profiles(id)
);

INSERT INTO profiles (id, first_name, last_name, email) VALUES
(1, 'Wallis', 'Treadway', 'wtreadway0@senate.gov'),
(2, 'Franklin', 'Blackston', 'fblackston1@parallels.com'),
(3, 'Honoria', 'Constant', 'hconstant2@umich.edu'),
(4, 'Bertine', 'Hillaby', 'bhillaby3@artisteer.com'),
(5, 'Constance', 'Knutsen', 'cknutsen4@google.ca');

INSERT INTO deals (profile_id, dt, amount) VALUES
(4, '2022-06-04', 22.31), (4, '2022-06-04', 36.33), (5, '2022-06-04', 21.41),
(1, '2022-06-07', 92.84), (4, '2022-06-08', 24.41), (3, '2022-06-13', 61.55),
(4, '2022-06-16', 77.70), (5, '2022-06-18', 58.79), (4, '2022-06-20', 43.61),
(3, '2022-06-22', 10.41), (1, '2022-06-23', 6.59), (1, '2022-06-30', 43.07);

SELECT 
p.first_name,
p.last_name,
p.email,
SUM(d.amount) AS total
FROM profiles p 
JOIN deals d ON p.id = d.profile_id
WHERE
d.dt BETWEEN '2022-06-01' AND '2022-06-31'
GROUP BY
p.first_name,
p.last_name,
p.email
ORDER BY
total DESC
LIMIT 3;



62. CREATE TABLE profiles (
    id SMALLINT PRIMARY KEY,
    first_name VARCHAR(255),
    last_name VARCHAR(255),
    email VARCHAR(255),
    is_verified SMALLINT
);

CREATE TABLE stats (
    profile_id SMALLINT,
    job_success_score SMALLINT,
    FOREIGN KEY (profile_id) REFERENCES profiles(id)
);

INSERT INTO profiles (id, first_name, last_name, email, is_verified) VALUES
(1, 'Junia', 'Sehorsch', 'jsehorsch0@oracle.com', 0),
(4, 'Kendell', 'Sylvester', 'ksylvester3@canalblog.com', 1),
(5, 'Koralle', 'Ragsdale', 'kragsdale4@buzzfeed.com', 1),
(10, 'Sophie', 'Messenger', 'smessenger9@myspace.com', 1),
(12, 'Kayle', 'Jesteco', 'kjestecob@ocn.ne.jp', 1),
(13, 'Munroe', 'Chevolleau', 'mchevolleauc@yandex.ru', 1),
(14, 'Etheline', 'Choake', 'echoaked@hao123.com', 1),
(18, 'Margo', 'Finnemore', 'mfinnemoreh@discovery.com', 1),
(20, 'Demetris', 'Arnet', 'darnetj@livejournal.com', 1),
(17, 'Jori', 'MacFaell', 'jmacfaellg@va.gov', 1),
(7, 'Harold', 'Molloy', 'hmolloy6@ycombinator.com', 1);

INSERT INTO stats (profile_id, job_success_score) VALUES
(4, 100), (5, 95), (10, 95), (12, 95), (13, 95), (14, 95),
(18, 95), (20, 90), (17, 90), (7, 90);

SELECT 
p.first_name,
p.last_name,
p.email,
s.job_success_score
FROM 
profiles p 
JOIN 
stats s ON p.id = s.profile_id
GROUP BY
p.first_name,
p.last_name,
p.email,
s.job_success_score,
p.is_verified
HAVING
s.job_success_score >= 90
AND is_verified = 1
ORDER BY
s.job_success_score DESC,
p.first_name ASC,
p.last_name ASC
LIMIT 10;



63. CREATE TABLE configurations (
    id VARCHAR(64) PRIMARY KEY,
    name VARCHAR(255)
);

CREATE TABLE deployments (
    configuration_id VARCHAR(64),
    dt VARCHAR(19),
    FOREIGN KEY (configuration_id) REFERENCES configurations(id)
);

INSERT INTO configurations (id, name) VALUES
('1vcpu_512mb_10gb_500gb', '1 CPU / 512 MB RAM / 10 GB SSD Disk / 500 GB transfer'),
('1vcpu_1gb_25gb_1tb', '1 CPU / 1 GB RAM / 25 GB SSD Disk / 1000 GB transfer'),
('1vcpu_2gb_50gb_2tb', '1 CPU / 2 GB RAM / 50 GB SSD Disk / 2 TB transfer'),
('2vcpu_2gb_60gb_3tb', '2 CPUs / 2 GB RAM / 60 GB SSD Disk / 3 TB transfer'),
('2vcpu_4gb_80gb_4tb', '2 CPUs / 4 GB RAM / 80 GB SSD Disk / 4 TB transfer'),
('4vcpu_8gb_160gb_5tb', '4 CPUs / 8 GB RAM / 160 GB SSD Disk / 5 TB transfer'),
('8vcpu_16gb_320gb_6tb', '8 CPUs / 16 GB RAM / 320 GB SSD Disk / 6 TB transfer');

INSERT INTO deployments (configuration_id, dt) VALUES
('2vcpu_2gb_60gb_3tb', '2021-01-24'),
('4vcpu_8gb_160gb_5tb', '2021-01-25'),
('2vcpu_2gb_60gb_3tb', '2021-02-08'),
('1vcpu_512mb_10gb_500gb', '2021-03-25'),
('8vcpu_16gb_320gb_6tb', '2021-03-26'),
('2vcpu_2gb_60gb_3tb', '2021-05-24'),
('1vcpu_512mb_10gb_500gb', '2021-05-31'),
('1vcpu_2gb_50gb_2tb', '2021-08-25'),
('2vcpu_4gb_80gb_4tb', '2021-09-05'),
('2vcpu_4gb_80gb_4tb', '2021-09-23'),
('2vcpu_4gb_80gb_4tb', '2021-09-28'),
('2vcpu_2gb_60gb_3tb', '2021-10-14'),
('1vcpu_2gb_50gb_2tb', '2021-11-01'),
('1vcpu_512mb_10gb_500gb', '2021-11-26'),
('1vcpu_1gb_25gb_1tb', '2021-12-27');

SELECT 
c.name AS configuration,
COUNT(d.configuration_id) AS deployments
FROM 
configurations c 
JOIN 
deployments d ON c.id = d.configuration_id
WHERE 
d.dt LIKE '2021%'
GROUP BY
c.name
ORDER BY
deployments DESC;



64. CREATE TABLE events (
    dt VARCHAR(19),
    type VARCHAR(64)
);

INSERT INTO events (dt, type) VALUES
('2022-05-27 16:12:50', 'buy'),
('2022-05-20 09:09:07', 'buy'),
('2022-05-22 09:06:37', 'buy'),
('2022-05-31 07:49:36', 'buy'),
('2022-05-14 22:29:10', 'buy'),
('2022-05-13 15:00:54', 'sell'),
('2022-05-24 15:40:54', 'sell'),
('2022-05-13 01:20:05', 'sell'),
('2022-05-16 07:07:44', 'sell'),
('2022-05-01 16:57:00', 'sell');

SELECT 
COUNT(e.type) AS purchases
FROM 
events e 
WHERE
e.dt LIKE '2022-05%'
GROUP BY
e.type
HAVING
e.type = 'buy'
ORDER BY
purchases ASC;



65. CREATE TABLE companies (
    id SMALLINT PRIMARY KEY,
    name VARCHAR(255)
);

CREATE TABLE campaigns (
    company_id SMALLINT,
    expenses DECIMAL(7,2),
    revenue DECIMAL(7,2),
    FOREIGN KEY (company_id) REFERENCES companies(id)
);

INSERT INTO companies (id, name) VALUES
(1, 'Lion Biotechnologies, Inc.'),
(2, 'Boston Private Financial Holdings, Inc.'),
(3, 'Universal Corporation'),
(4, 'Arbutus Biopharma Corporation'),
(5, 'Royal Bank Of Canada'),
(6, 'Penn West Petroleum Ltd'),
(7, 'Public Storage'),
(8, 'Halcon Resources Corporation'),
(9, 'TTM Technologies, Inc.'),
(10, 'Atwood Oceanics, Inc.'),
(11, 'ACADIA Pharmaceuticals Inc.'),
(12, 'Central European Media Enterprises Ltd.'),
(13, 'Oxbridge Re Holdings Limited'),
(14, 'Western Refining Logistics, LP'),
(15, 'Vaalco Energy Inc'),
(16, 'Xilinx, Inc.'),
(17, 'Liberty Global plc'),
(18, 'Honda Motor Company, Ltd.'),
(19, 'Great Plains Energy Inc'),
(20, 'Assurant, Inc.');

INSERT INTO campaigns (company_id, expenses, revenue) VALUES
(1, 7390.24, 8652.18), (2, 5774.65, 7955.47), (3, 2154.71, 5920.23),
(4, 9366.49, 3397.85), (5, 2765.18, 9158.63), (6, 7908.41, 5018.85),
(7, 2251.44, 6654.52), (8, 3383.14, 9354.79), (9, 8287.96, 9522.53),
(10, 4356.62, 4658.52), (11, 9272.86, 9161.77), (12, 4996.18, 5903.57),
(13, 8354.75, 2259.26), (14, 6402.90, 8146.16), (15, 1692.05, 686.71),
(16, 5988.48, 9089.41), (17, 6192.33, 7580.19), (18, 3016.37, 7761.25),
(19, 9838.05, 1293.09), (20, 4386.52, 9513.73);


SELECT
c.name,
(ca.revenue - ca.expenses) AS profit
FROM 
companies c
JOIN
campaigns ca ON c.id = ca.company_id
GROUP BY
c.name,
ca.revenue,
ca.expenses
ORDER BY
profit DESC
LIMIT 3;



66. CREATE TABLE clients (
    mac VARCHAR(64),
    upstream_rate INT,
    downstream_rate INT,
    downtime_rate VARCHAR(64)
);
INSERT INTO clients (mac, upstream_rate, downstream_rate, downtime_rate) VALUES
('78-C1-E5-20-D5-61', 925526, 5195, 'never'),
('78-E2-20-71-9C-30', 582152, 375829, 'never'),
('0D-09-F7-77-03-E5', 359529, 710743, 'never'),
('56-18-67-55-58-EA', 78626, 562544, 'once'),
('C9-73-EC-1C-4C-B7', 574927, 669655, 'yearly'),
('02-35-3F-7B-CC-76', 430072, 296196, 'seldom'),
('3D-95-33-8A-65-F9', 894176, 489401, 'monthly'),
('8D-33-3F-0E-04-D5', 897666, 297063, 'weekly'),
('0E-0A-63-B9-79-3E', 69133, 984354, 'seldom'),
('15-D3-2A-DD-02-A4', 19203, 995983, 'seldom'),
('8D-42-B1-97-AB-87', 476648, 177677, 'monthly'),
('6B-64-60-47-16-D3', 700056, 374321, 'monthly'),
('FD-61-81-00-BF-EC', 216401, 498229, 'yearly'),
('95-46-C6-C7-6F-E0', 236331, 341013, 'monthly'),
('11-0E-62-32-62-5E', 694746, 451525, 'daily'),
('D6-5B-72-D5-FF-4F', 2931, 992852, 'monthly'),
('66-BF-AD-F2-E5-45', 861075, 44216, 'monthly'),
('E5-C9-C6-74-2E-A8', 639487, 968494, 'daily'),
('18-56-3A-93-8E-9F', 494945, 259910, 'weekly'),
('51-EB-D7-22-45-99', 219419, 326479, 'often');


SELECT 
c.mac,
c.upstream_rate,
c.downstream_rate,
c.downtime_rate
FROM 
clients c 
WHERE
(c.downstream_rate > c.upstream_rate) AND c.downtime_rate IN ('never','once')
GROUP BY
c.mac,
c.upstream_rate,
c.downstream_rate,
c.downtime_rate
ORDER BY
c.mac ASC;



67. CREATE TABLE owners (
    id SMALLINT PRIMARY KEY,
    full_name VARCHAR(255),
    email_address VARCHAR(255),
    on_vacation SMALLINT
);

CREATE TABLE events (
    owner_id SMALLINT,
    dt VARCHAR(19),
    title VARCHAR(255)
);


INSERT INTO owners (id, full_name, email_address, on_vacation) VALUES
(1, 'Benjamin Sevier', 'bsevier0@discuz.net', 1),
(2, 'Aleksandr Fellows', 'afellows1@instagram.com', 0),
(3, 'Collete Pack', 'cpack2@mit.edu', 0),
(4, 'Lorelle Squibb', 'lsquibb3@huffingtonpost.com', 0),
(5, 'Cointon Welberry', 'cwelberry4@theguardian.com', 1);

INSERT INTO events (owner_id, dt, title) VALUES
(1, '2021-08-13 09:17:41', 'ac consequat metus sapien ut'),
(1, '2021-10-26 16:14:28', 'a libero nam'),
(1, '2022-05-18 16:09:36', 'vestibulum ante ipsum primis in faucibus'),
(1, '2022-06-04 20:37:36', 'nisi volutpat eleifend donec'),
(2, '2021-01-17 23:34:59', 'eu mi nulla ac'),
(2, '2021-01-26 15:59:04', 'nisl venenatis lacinia'),
(2, '2021-04-25 05:04:29', 'arcu adipiscing molestie hendrerit'),
(2, '2022-02-22 10:24:50', 'lectus vestibulum quam sapien varius'),
(2, '2022-05-22 03:04:33', 'pellentesque quisque porta volutpat erat'),
(3, '2021-03-11 08:25:21', 'felis sed interdum venenatis'),
(3, '2022-02-15 07:29:45', 'nulla sed accumsan felis ut'),
(4, '2021-02-28 16:10:55', 'cras mi pede malesuada in imperdiet et'),
(4, '2021-05-20 03:35:11', 'odio in hac habitasse platea dictumst'),
(4, '2021-09-01 19:25:41', 'sagittis nam congue risus semper'),
(4, '2022-02-04 03:11:53', 'luctus cum sociis natoque penatibus et magnis'),
(4, '2022-06-18 08:39:41', 'nec molestie sed justo pellentesque'),
(5, '2021-04-04 13:39:38', 'id massa id nisl venenatis lacinia aenean'),
(5, '2021-10-18 19:04:14', 'adipiscing lorem vitae mattis'),
(5, '2021-10-19 00:56:11', 'vestibulum eget vulputate ut ultrices vel augue'),
(5, '2022-04-26 18:55:04', 'at dolor quis');

SELECT 
e.dt,
e.title,
o.full_name,
o.email_address
FROM 
owners o 
JOIN 
events e ON o.id = e.owner_id
WHERE
o.on_vacation != 1
GROUP BY
e.dt,
e.title,
o.full_name,
o.email_address
ORDER BY
e.dt ASC
LIMIT 5;




68. CREATE TABLE clients (
    id SMALLINT,
    mac VARCHAR(64)
);

CREATE TABLE traffic (
    client_id SMALLINT,
    amount INT
);
-- clients
INSERT INTO clients (id, mac) VALUES
(1, 'E5-A3-AC-8A-20-F9'),
(2, '3B-2F-83-25-A8-81'),
(3, '3A-4E-A6-43-1D-B1'),
(4, 'B7-03-14-91-8F-58'),
(5, '63-1A-FD-9A-AF-6F'),
(6, 'D6-B8-1F-1D-34-04'),
(7, '38-83-E5-F8-C8-DC'),
(8, 'E9-F6-89-7F-8D-34'),
(9, '82-2E-B3-67-04-41'),
(10, 'D1-9D-DE-37-A0-49'),
(11, '8A-46-F4-83-29-13'),
(12, '4C-1F-7B-C7-08-7E'),
(13, '72-57-E6-CA-2C-91'),
(14, '57-F7-E7-E7-45-36'),
(15, '05-8A-05-1D-2D-20'),
(16, '46-06-F1-B9-65-7C'),
(17, '0A-E0-26-9D-2A-27'),
(18, 'F0-86-99-18-36-9B'),
(19, 'DD-81-BF-53-BD-9B'),
(20, '50-53-64-8E-42-BE');

-- traffic
INSERT INTO traffic (client_id, amount) VALUES
(3, 6385047),
(8, 6490817),
(14, 9109219),
(16, 5558512),
(17, 1870152),
(17, 8228920),
(18, 326127),
(18, 5429741),
(18, 4063477),
(19, 7411789),
(20, 5832337),
(20, 1426585),
(23, 1097368),
(23, 6769594),
(23, 802387),
(24, 5959513),
(24, 1300408),
(24, 4631624),
(28, 1629306),
(29, 2814818);

SELECT 
c.mac
FROM clients c 
JOIN traffic t ON c.id = client_id
GROUP BY
c.mac
ORDER BY
c.mac ASC;


69. CREATE TABLE wallets (
    id SMALLINT PRIMARY KEY,
    address VARCHAR(64)
);

CREATE TABLE transactions (
    wallet_id SMALLINT,
    credit DECIMAL(4,2)
);

INSERT INTO wallets (id, address) VALUES
(1, '0x1ecc4cefde6dfb773352a2dcd8b5f518ccd24'),
(2, '0x0f4487168610dcae7f16b6c000a7ba284bb6703c'),
(3, '0x54c5c516b5c601a3e6fdea18db966186274879e6'),
(4, '0x50c404a6790d44849c3500d95f147d2102db5f77'),
(5, '0x41cc32037fdcc6cbf5cb04ada4e38032a84361ac'),
(6, '0xb38ca2076b2c3a0a1c0796ccab5dc3fcbb645336'),
(7, '0x923c6ee4debce6c37c512eb90e9b26fc9bbfc3e1'),
(8, '0x76cfbf67e1765117a98fd2286bdbcb731b0d29ec'),
(9, '0xf4d43df89365453440dbeb5e53445d3400d218'),
(10, '0x010a4b23f985eda6b397864878cb70bf0b233aa6'),
(11, '0x106c4c852394d5a6580c11ca0060687a798ec78c'),
(12, '0x541da74b9f4b2283524a65156263bcce7429ba61'),
(13, '0x86df8e62ad23a35ee2e64b3b4b128d8a3660116a'),
(14, '0x6b021b12b779aec27009f18bd6ad227685df5474'),
(15, '0x7073e9f48b7211d9940db4c8bad8e31d5d9d0577'),
(16, '0x4672e132b9627a7db76e5af431bb5febc93b1b2f'),
(17, '0x5cbc82a01d3b9df8f50ee09bf5a1c48afe0cb966'),
(18, '0x1c7d30f90081b45004e0d6549534ef8658795672'),
(19, '0xf830618ca9f027ce5f23bf270629a6a418ad355c'),
(20, '0x1a31ac1b923fcc17a42a340091424ea18192a3e7');

INSERT INTO transactions (wallet_id, credit) VALUES
(3, 5.21),
(6, 24.04),
(8, 29.66),
(10, 1.67), (10, 3.32), (10, 4.27),
(14, 3.32), (14, 3.35),
(15, 14.34),
(16, 11.94),
(20, 13.86), (20, 16.54),
(21, 11.64),
(25, 12.86),
(26, 8.77), (26, 12.36),
(27, 29.65), 
(28, 3.00), (28, 13.26), (28, 2.85);

SELECT 
w.address
FROM 
wallets w
JOIN 
transactions t ON w.id = t.wallet_id
WHERE
t.credit >= 1
GROUP BY
w.address
ORDER BY
w.address ASC;



70. CREATE TABLE animals (
    id SMALLINT PRIMARY KEY,
    name VARCHAR(64)
);

CREATE TABLE tracklog (
    animal_id SMALLINT,
    tracked_at VARCHAR(19)
);

INSERT INTO animals (id, name) VALUES
(1, 'Blue and yellow macaw'),
(2, 'Jungle kangaroo'),
(3, 'Stork, woolly-necked'),
(4, 'North American river otter'),
(5, 'Square-lipped rhinoceros'),
(6, 'Black-fronted bulbul'),
(7, 'American beaver'),
(8, 'Capybara'),
(9, 'Black-backed jackal'),
(10, 'Dragon, ornate rock'),
(11, 'Wombat, southern hairy-nosed'),
(12, 'Snake, carpet'),
(13, 'Egyptian cobra'),
(14, 'Green heron'),
(15, 'Indian star tortoise'),
(16, 'Roan antelope'),
(17, 'Rhea, common'),
(18, 'Fairy penguin'),
(19, 'Black-eyed bulbul'),
(20, 'Starling, cape');

INSERT INTO tracklog (animal_id, tracked_at) VALUES
(2, '2021-07-08 12:30:34'),
(5, '2021-09-15 03:00:04'),
(8, '2021-12-14 11:20:50'),
(9, '2021-05-15 14:54:15'),
(11, '2021-07-03 17:04:14'),
(11, '2021-02-23 09:02:11'),
(15, '2021-11-02 22:49:37'),
(15, '2021-07-31 04:51:25'),
(15, '2021-06-06 14:57:23'),
(19, '2021-03-25 02:00:31'),
(19, '2021-04-21 22:02:51'),
(19, '2021-07-17 17:12:35'),
(21, '2021-05-06 03:35:59'),
(22, '2021-09-21 22:49:36'),
(22, '2021-12-04 00:26:29'),
(24, '2021-12-27 08:19:52'),
(26, '2021-05-14 07:30:11'),
(28, '2021-06-08 00:47:00'),
(28, '2021-04-24 00:42:31'),
(30, '2021-05-06 21:03:03');

SELECT 
a.name
FROM 
animals a 
JOIN 
tracklog t ON a.id = t.animal_id
GROUP BY
a.name
ORDER BY
a.name ASC;




71. CREATE TABLE CUSTOMERS (
    id INT,
    customer_name VARCHAR(30)
);

CREATE TABLE ORDERS (
    order_id INT,
    customer_id INT,
    order_type VARCHAR(5),
    order_amount DECIMAL(18,2)
);

INSERT INTO CUSTOMERS (id, customer_name) VALUES
(401, 'Hubert Keesler'),
(402, 'Devin Vert'),
(403, 'Lashawna Bowerman'),
(404, 'Brigid Wellborn'),
(405, 'Josefine Perl');

INSERT INTO ORDERS (order_id, customer_id, order_type, order_amount) VALUES
(4361, 401, 'Sell', 912.77),
(3478, 405, 'Sell', 741.69),
(7292, 405, 'Sell', 436.05),
(5833, 405, 'Sell', 231.30),
(3472, 402, 'Buy', 950.92),
(4472, 401, 'Sell', 367.70),
(2624, 404, 'Buy', 218.15),
(7198, 405, 'Buy', 797.29),
(7660, 403, 'Buy', 131.18),
(5192, 401, 'Buy', 362.44),
(5260, 402, 'Buy', 636.26),
(2726, 403, 'Sell', 138.15),
(6594, 401, 'Buy', 234.51),
(4657, 404, 'Buy', 427.30),
(9744, 402, 'Sell', 623.36);

SELECT 
c.customer_name,
ROUND(SUM( 
CASE 
WHEN o.order_type = 'Buy' THEN o.order_amount * 0.001  
WHEN o.order_type = 'Sell' THEN o.order_amount * 0.0015 
ELSE 0 
END
), 2) AS total_fees
FROM 
customers c 
JOIN
orders o ON c.id = o.customer_id
GROUP BY
c.customer_name
ORDER BY
c.customer_name;



72. CREATE TABLE CREDIT_HOLDERS (
    id INT PRIMARY KEY,
    first_name VARCHAR(15),
    last_name VARCHAR(15),
    interest_rate INT
);

CREATE TABLE TRANSACTIONS (
    transaction_id INT PRIMARY KEY,
    credit_holder_id INT,
    amount DECIMAL(18,2),
    FOREIGN KEY (credit_holder_id) REFERENCES CREDIT_HOLDERS(id)
);

INSERT INTO CREDIT_HOLDERS (id, first_name, last_name, interest_rate) VALUES
(101, 'Clemencia', 'Hutsell', 12),
(102, 'Susannah', 'Ismail', 18),
(103, 'Sixta', 'Hagy', 18),
(104, 'Otto', 'Izquierdo', 18),
(105, 'Anita', 'Degroot', 15);


INSERT INTO TRANSACTIONS (transaction_id, credit_holder_id, amount) VALUES
(4361, 101, 65.22),
(3478, 104, 51.85),
(7292, 104, 64.60),
(5833, 105, 72.15),
(3472, 102, 96.28),
(4472, 101, 80.06),
(2624, 101, 85.27),
(7198, 104, 23.73),
(7660, 103, 81.86),
(5192, 101, 69.64),
(5260, 101, 71.72),
(2726, 102, 57.66),
(6594, 103, 23.23),
(4657, 101, 81.68),
(9744, 104, 99.57),
(2054, 103, 51.13),
(7156, 105, 12.78),
(3273, 105, 36.15),
(9756, 101, 45.41),
(9702, 105, 69.75);

SELECT 
CONCAT(c.first_name, '',  c.last_name) AS full_name,
ROUND(SUM(t.amount) * (1 + c.interest_rate / 100.0),
2) AS dues
FROM 
credit_holders c
JOIN 
transactions t ON c.id = t.credit_holder_id
WHERE
c.interest_rate > 12
GROUP BY
c.id,
c.first_name,
c.last_name
ORDER BY
dues DESC;




73. CREATE TABLE accounts (
    id INT PRIMARY KEY,
    account_holder VARCHAR(30),
    amount VARCHAR(10)
);

INSERT INTO accounts (id, account_holder, amount) VALUES
(1, 'Ellis Beane', '$5582.03'),
(2, 'Drew Nolf', '$2470.3'),
(3, 'Jordan Chatmon', '$6211.52'),
(4, 'Robin Hansard', '$8133.31'),
(5, 'Spencer Days', '$5273.81'),
(6, 'Morgan Criss', '$3741.85'),
(7, 'Wesley Waugh', '$7056.14'),
(8, 'Alex Canty', '$2590.45'),
(9, 'Blake Hawbaker', '$3987.27'),
(10, 'Taylor Blackston', '$8351.98');


SELECT
a.account_holder,
CONCAT(LEFT(a.amount, 1), TO_CHAR(CAST(SUBSTRING(a.amount, 2) AS NUMERIC) * 0.05, 'FM999999999.00')) AS interest
FROM 
accounts a 
ORDER BY 
a.account_holder;



74. CREATE TABLE transactions (
    transaction_id VARCHAR(10) PRIMARY KEY,
    amount DECIMAL(18,2)
);

INSERT INTO transactions (transaction_id, amount) VALUES
('19SEP2187', 785.72),
('19OCT4361', 752.64),
('19APR3478', 197.92),
('19SEP7292', 910.26),
('21MAR5833', 344.70),
('20MAY3472', 939.61),
('20DEC4472', 154.98),
('20DEC2624', 935.44),
('21JUN7198', 309.81),
('19APR7660', 528.10),
('20MAR5192', 995.22),
('19OCT5260', 861.11),
('21JUN2726', 611.94),
('19OCT6594', 478.54),
('19APR4657', 183.20);

SELECT
    SUBSTRING(transaction_id, 1, 2) AS year,
    SUBSTRING(transaction_id, 3, 3) AS month,
    SUM(amount) AS total_transactions
FROM 
    transactions
GROUP BY 
    SUBSTRING(transaction_id, 1, 2),
    SUBSTRING(transaction_id, 3, 3)
ORDER BY 
    SUBSTRING(transaction_id, 1, 2),
    CASE SUBSTRING(transaction_id, 3, 3)
        WHEN 'JAN' THEN 1
        WHEN 'FEB' THEN 2
        WHEN 'MAR' THEN 3
        WHEN 'APR' THEN 4
        WHEN 'MAY' THEN 5
        WHEN 'JUN' THEN 6
        WHEN 'JUL' THEN 7
        WHEN 'AUG' THEN 8
        WHEN 'SEP' THEN 9
        WHEN 'OCT' THEN 10
        WHEN 'NOV' THEN 11
        WHEN 'DEC' THEN 12
    END;


75. CREATE TABLE RESULTS (
    id INT,
    first_name VARCHAR(20),
    last_name VARCHAR(20),
    cgpa_first_year FLOAT,
    cgpa_second_year FLOAT,
    cgpa_third_year FLOAT,
    cgpa_fourth_year FLOAT
);

INSERT INTO results (
    id, first_name, last_name, 
    cgpa_first_year, cgpa_second_year, cgpa_third_year, cgpa_fourth_year
) VALUES
(1, 'Pearlene', 'Beane', 7.0, 5.1, 8.4, 8.9),
(2, 'Franklin', 'Nolf', 7.7, 7.2, 5.2, 8.3),
(3, 'Bell', 'Chatmon', 7.3, 8.4, 8.9, 10.0),
(4, 'Belva', 'Hansard', 6.2, 9.2, 5.8, 6.7),
(5, 'Missy', 'Days', 8.3, 10.0, 7.3, 6.7),
(6, 'Vicenta', 'Criss', 5.4, 9.5, 6.1, 9.0),
(7, 'Annelle', 'Waugh', 6.5, 7.9, 9.6, 9.3),
(8, 'Darby', 'Canty', 5.5, 9.8, 6.5, 9.0),
(9, 'Ka', 'Hawbaker', 5.7, 6.4, 5.2, 6.8),
(10, 'Alease', 'Blackston', 5.3, 7.5, 9.3, 6.0);

SELECT
  CONCAT(r.first_name, ' ', r.last_name) AS full_name,
  ROUND((
    r.cgpa_first_year + r.cgpa_second_year + r.cgpa_third_year + r.cgpa_fourth_year
  )::numeric / 4, 1) AS average_gpa
FROM 
  results r
ORDER BY 
  average_gpa DESC;



76. CREATE TABLE funds (
  id INT PRIMARY KEY,
  order_date DATE,
  fund_name VARCHAR(50),
  order_amount INT
);

INSERT INTO funds (id, order_date, fund_name, order_amount) VALUES
(1, '2021-09-05', 'Mid-Cap', 151),
(2, '2020-11-27', 'Small-Cap', 784),
(3, '2020-11-22', 'Multi-Cap', 761),
(4, '2020-02-26', 'Large-Cap', 778),
(5, '2020-01-04', 'Mid-Cap', 949),
(6, '2020-02-01', 'Large-Cap', 392),
(7, '2020-02-07', 'Mid-Cap', 629),
(8, '2020-06-01', 'Mid-Cap', 529),
(9, '2020-08-05', 'Large-Cap', 258),
(10, '2021-09-23', 'Mid-Cap', 739),
(11, '2020-02-14', 'Large-Cap', 563),
(12, '2019-09-29', 'Small-Cap', 817),
(13, '2019-05-11', 'Large-Cap', 121),
(14, '2019-09-18', 'Mid-Cap', 341),
(15, '2019-07-07', 'Large-Cap', 260),
(16, '2018-10-11', 'Small-Cap', 102),
(17, '2018-06-04', 'Mid-Cap', 496),
(18, '2017-07-26', 'Multi-Cap', 321),
(19, '2020-04-11', 'Small-Cap', 323),
(20, '2020-09-24', 'Multi-Cap', 938),
(21, '2020-06-16', 'Small-Cap', 668),
(22, '2021-03-08', 'Small-Cap', 733),
(23, '2021-04-27', 'Small-Cap', 597),
(24, '2020-01-11', 'Small-Cap', 767),
(25, '2021-11-14', 'Small-Cap', 489);

SELECT 
EXTRACT(month FROM f.order_date) AS month,
f.fund_name,
SUM(f.order_amount) AS total_investments
FROM
funds f
GROUP BY
month,
fund_name,
EXTRACT(month FROM f.order_date)
ORDER BY
month,
fund_name;



77. CREATE TABLE traffic (
  client VARCHAR(17),
  protocol VARCHAR(64),
  traffic_in INT,
  traffic_out INT
);

INSERT INTO traffic (client, protocol, traffic_in, traffic_out) VALUES
('02-E1-80-76-EC-4B', 'BGP', 0, 234737),
('43-15-AA-26-0F-A4', 'BGP', 402860, 606565),
('90-E7-D6-14-7E-8C', 'BGP', 840772, 988197),
('FB-60-23-C1-5E-D6', 'DNS', 341155, 356569),
('4D-6D-7F-62-F4-00', 'FTP', 8346, 413322),
('09-B9-A6-46-C4-21', 'FTP', 210656, 470568),
('B1-A6A35-F2A-C2', 'FTP', 897097, 161083),
('OC-CA-68-D2-4B-F5', 'HTTP', 918793, 550403),
('A4-C6-52-01-29-C', 'HTTPS', 520856, 185387),
('95-BB-78-B0-64-2', 'POP', 150880, 423073),
('B9-C1-1B-32-55-95', 'POP', 862946, 979544),
('14-FD-F1-6E-56-7', 'SMTP', 139389, 280646),
('70-E1-2D-B1-2B-9B', 'SMTP', 163986, 450401),
('C6-F1-59-FF-5D-BE', 'SMTP', 271295, 878246),
('62-C1-0F-DA-32-A7', 'SMTP', 388933, 81625),
('41-F0-8F-B8-61-D9', 'SMTP', 752842, 253981),
('93-A3-F0-1S-75-FA', 'SSH', 496717, 599280),
('52-F2-BF-45-BA-74', 'SSH', 632534, 128765),
('87-66-B5-A5-2F-7B', 'SSH', 835441, 354950),
('CE-FC-80-F3-95-58', 'UDP', 903443, 120298);


SELECT 
t.protocol,
SUM(t.traffic_in) AS traffic_in,
SUM(t.traffic_out) AS traffic_out
FROM traffic t
GROUP BY
t.protocol
HAVING
SUM(t.traffic_in) > SUM(t.traffic_out)
ORDER BY
t.protocol ASC;



78. CREATE TABLE customers (
  id SMALLINT PRIMARY KEY,
  first_name VARCHAR(64),
  last_name VARCHAR(64)
);

CREATE TABLE campaigns (
  id SMALLINT PRIMARY KEY,
  customer_id SMALLINT,
  name VARCHAR(64),
  FOREIGN KEY (customer_id) REFERENCES customers(id)
);

CREATE TABLE events (
  dt VARCHAR(19),
  campaign_id SMALLINT,
  status VARCHAR(64),
  FOREIGN KEY (campaign_id) REFERENCES campaigns(id)
);

INSERT INTO customers (id, first_name, last_name) VALUES
(1, 'Whitney', 'Ferrero'),
(2, 'Dickie', 'Romera');

INSERT INTO campaigns (id, customer_id, name) VALUES
(1, 1, 'Upton Group'),
(2, 1, 'Roob, Hudson and Rippin'),
(3, 1, 'McCullough, Rempel and Larson'),
(4, 1, 'Lang and Sons'),
(5, 2, 'Ruecker, Hand and Haley');

INSERT INTO events (dt, campaign_id, status) VALUES
('2021-12-02 13:52:00', 1, 'failure'),
('2021-12-02 08:17:48', 2, 'failure'),
('2021-12-02 08:18:17', 2, 'failure'),
('2021-12-01 11:55:32', 3, 'failure'),
('2021-12-01 06:53:16', 4, 'failure'),
('2021-12-02 04:51:09', 4, 'failure'),
('2021-12-01 06:34:04', 5, 'failure'),
('2021-12-02 03:21:18', 5, 'failure'),
('2021-12-01 03:18:24', 5, 'failure'),
('2021-12-02 15:32:37', 1, 'success'),
('2021-12-01 04:23:20', 1, 'success'),
('2021-12-02 06:53:24', 1, 'success'),
('2021-12-02 08:01:02', 2, 'success'),
('2021-12-02 15:57:19', 2, 'success'),
('2021-12-02 16:14:34', 3, 'success'),
('2021-12-03 05:54:43', 4, 'success'),
('2021-12-03 17:56:45', 4, 'success'),
('2021-12-03 11:56:50', 4, 'success'),
('2021-12-03 06:08:20', 5, 'success');


SELECT
CONCAT(c.first_name, ' ', c.last_name) AS customer,
COUNT(*) AS failures
FROM 
customers c 
JOIN campaigns ca ON c.id = ca.customer_id
JOIN events e ON ca.id = e.campaign_id
WHERE
e.status = 'failure'
GROUP BY
c.id,
c.first_name,
c.last_name
HAVING
COUNT(*) > 3
ORDER BY
failures;



79. CREATE TABLE candidates (
  id SMALLINT PRIMARY KEY,
  first_name VARCHAR(64),
  last_name VARCHAR(64)
);

CREATE TABLE results (
  candidate_id SMALLINT,
  vote_at VARCHAR(19),
  FOREIGN KEY (candidate_id) REFERENCES candidates(id)
);

INSERT INTO candidates (id, first_name, last_name) VALUES
(1, 'Xavier', 'Ping'),
(2, 'Westley', 'Drewell'),
(3, 'Dominick', 'Scoble');

INSERT INTO results (candidate_id, vote_at) VALUES
(1, '2021-12-01 03:55:23'),
(1, '2021-12-01 21:53:26'),
(1, '2021-12-02 07:57:40'),
(1, '2021-12-02 13:56:06'),
(2, '2021-12-01 11:46:40'),
(2, '2021-12-01 14:56:05'),
(2, '2021-12-01 21:54:50'),
(2, '2021-12-02 00:43:18'),
(2, '2021-12-02 06:59:33'),
(2, '2021-12-02 08:36:35'),
(2, '2021-12-02 10:20:33'),
(2, '2021-12-02 14:02:38'),
(3, '2021-12-01 05:18:34'),
(3, '2021-12-02 03:55:37'),
(3, '2021-12-02 05:30:24'),
(3, '2021-12-02 08:32:06');


SELECT
CONCAT(c.first_name, ' ', c.last_name) AS candidate,
COUNT(*) AS votes
FROM 
candidates c
JOIN 
results r ON c.id = r.candidate_id
GROUP BY
c.id, 
c.first_name, 
c.last_name
ORDER BY
votes DESC,
candidate ASC;




80. CREATE TABLE events (
  dt VARCHAR(19),
  customer VARCHAR(64),
  amount DECIMAL(5,2)
);

INSERT INTO events (dt, customer, amount) VALUES
('2021-11-22 06:41:01', 'Donaugh Furneaux', 0.89),
('2021-12-22 20:07:04', 'Donaugh Furneaux', 10.51),
('2021-12-31 05:22:11', 'Donaugh Furneaux', 55.92),
('2021-12-12 21:26:42', 'Harley Lyddiard', 37.68),
('2021-11-22 21:24:30', 'Kippy Jelly', 85.87),
('2021-11-25 07:00:29', 'Kippy Jelly', 7.25),
('2021-12-16 16:48:32', 'Kippy Jelly', 65.49),
('2021-11-22 23:30:55', 'Latrina Jackman', 93.49),
('2021-11-24 19:38:52', 'Latrina Jackman', 82.28),
('2021-11-30 22:59:33', 'Latrina Jackman', 96.87),
('2021-12-30 13:05:34', 'Latrina Jackman', 88.19),
('2021-11-22 02:08:02', 'Maribel Braim', 20.19),
('2021-12-13 00:14:58', 'Maribel Braim', 97.99),
('2021-12-26 13:22:20', 'Maribel Braim', 57.06),
('2021-12-29 00:20:27', 'Maribel Braim', 24.35),
('2021-11-25 14:29:29', 'Orrin Curley', 6.69),
('2021-12-08 06:22:16', 'Orrin Curley', 36.85),
('2021-12-09 15:32:16', 'Orrin Curley', 11.04),
('2021-11-28 00:15:20', 'Rasla Venny', 14.59),
('2021-12-25 09:58:23', 'Rasla Venny', 6.41);


SELECT 
e.customer,
COUNT(*) AS transactions,
SUM(e.amount) AS customer
FROM 
events e 
WHERE
TO_CHAR(TO_TIMESTAMP(dt, 'YYYY-MM-DD HH24:MI:SS'), 'MM') 
= '12'
GROUP BY
e.customer
HAVING
COUNT(*) >= 3
ORDER BY
e.customer ASC;



81. CREATE TABLE marks (
  STUDENT_ID INTEGER,
  MARKS INTEGER
);

INSERT INTO marks (STUDENT_ID, MARKS) VALUES
(1, 450),
(2, 200),
(3, 260),
(2, 300),
(3, 250);

SELECT
m.student_id,
SUM(m.marks) AS sum_of_marks
FROM 
marks m
GROUP BY
m.student_id
HAVING
SUM(m.marks) >= '500'
ORDER BY
m.student_id DESC;



82. CREATE TABLE families (
  id VARCHAR(64),
  name VARCHAR(64),
  family_size INTEGER
);

CREATE TABLE countries (
  id VARCHAR(64),
  name VARCHAR(64),
  min_size INTEGER
);

INSERT INTO families (id, name, family_size) VALUES
('c00dac11bde74750b4d207b9c182a85f', 'Alex Thomas', 9),
('eb6f2d3f26694667e3e79dc274114a4', 'Chris Gray', 2);

INSERT INTO countries (id, name, min_size) VALUES
('023fd23615bd4ff4b2ae0a13ed7efec9', 'Bolivia', 2),
('be2477f32def04b210367cb26941fb9', 'Cook Islands', 4),
('3e85ab80a6f84ef3b9068b21dbcc54b3', 'Brazil', 4);


SELECT 
MAX(discounted_tours) AS max_discounted_tours
FROM(
SELECT
f.id,
COUNT(*) AS discounted_tours
FROM families f
JOIN
countries c ON f.family_size >= c.min_size
GROUP BY
f.id
) AS sub;



83. CREATE TABLE activities (
  id INTEGER PRIMARY KEY,
  name VARCHAR(64)
);

CREATE TABLE friends (
  id INTEGER PRIMARY KEY,
  name VARCHAR(64),
  activity VARCHAR(64)
);

INSERT INTO activities (id, name) VALUES
(1, 'Horse Riding'),
(2, 'Eating'),
(3, 'Playing');

INSERT INTO friends (id, name, activity) VALUES
(1, 'James Smith', 'Horse Riding'),
(2, 'Eric Jenkins', 'Eating'),
(3, 'Sean Cox', 'Eating'),
(4, 'Eric Schmidt', 'Horse Riding'),
(5, 'Chris Evans', 'Eating'),
(6, 'Jessica Breeds', 'Playing');



WITH ActivityCounts AS (
SELECT 
ACTIVITY,
COUNT(*) AS cnt
FROM
FRIENDS
GROUP BY
ACTIVITY
),
MinMax AS (
SELECT
MIN(cnt) AS min_cnt,
MAX(cnt) AS max_cnt
FROM
ActivityCounts
)
SELECT 
ACTIVITY
FROM
ActivityCounts, MinMax
WHERE
cnt 
NOT IN
(min_cnt, max_cnt);



SELECT a.name
FROM activities a
WHERE a.id NOT IN (
  (SELECT MIN(id) FROM activities),
  (SELECT MAX(id) FROM activities)
)
ORDER BY a.name;


84. CREATE TABLE CUSTOMERS (
    ID INTEGER PRIMARY KEY,
    NAME VARCHAR(100),
    PHONE VARCHAR(20),
    VISITED_ON DATE,
    AMOUNT INTEGER
);

INSERT INTO CUSTOMERS (ID, NAME, PHONE, VISITED_ON, AMOUNT) VALUES
(1, 'Julia', '1234567890', '2015-05-01', 100),
(2, 'Samantha', '1234567890', '2015-05-02', 200),
(3, 'Julia-Samantha', '1234567890', '2015-05-03', 300);


SELECT
c1.visited_on,
c1.amount,
(
SELECT AVG(c2.amount)
FROM CUSTOMERS c2
WHERE 
c2.visited_on BETWEEN
c1.visited_on - INTERVAL '6 DAY' AND c1.visited_on
) AS avg_amount
FROM 
CUSTOMERS c1
ORDER BY 
c1.visited_on;



85. CREATE TABLE STUDENT (
    ID INTEGER PRIMARY KEY,
    NAME VARCHAR(100)
);

CREATE TABLE EXAMINATION (
    STUDENT_ID INTEGER,
    SUBJECT VARCHAR(100)
);

INSERT INTO STUDENT (ID, NAME) VALUES
(1, 'Taylor'),
(2, 'Wesley'),
(3, 'Jordan'),
(4, 'Robin'),
(5, 'Alex');

INSERT INTO EXAMINATION (STUDENT_ID, SUBJECT) VALUES
(1, 'Biology'),
(1, 'Physics'),
(3, 'History'),
(4, 'Geography'),
(4, 'Geography');


SELECT 
e.student_id,
e.subject AS subject_name,
COUNT(*) AS number_of_times
FROM
examination e
GROUP BY
e.student_id,
e.subject;



86. CREATE TABLE CUSTOMER (
    ID INTEGER PRIMARY KEY,
    FIRST_NAME VARCHAR(100),
    LAST_NAME VARCHAR(100),
    COUNTRY VARCHAR(100),
    CREDIT_LIMIT FLOAT
);

INSERT INTO CUSTOMER (ID, FIRST_NAME, LAST_NAME, COUNTRY, CREDIT_LIMIT) VALUES
(1, 'Alix', 'White', 'USA', 200350.54),
(2, 'Tyler', 'Hanson', 'UK', 15354.23),
(3, 'Jordan', 'Fernandez', 'France', 359200.67),
(4, 'Drew', 'Bradley', 'Albania', 1001.57),
(5, 'Blake', 'Fuller', 'USA', 14789.00),
(6, 'Spencer', 'Johnston', 'China', 100243.35),
(7, 'Ellis', 'Gutierrez', 'USA', 9989999.20),
(8, 'Morgan', 'Thomas', 'Canada', 500500.23),
(9, 'Riley', 'Garza', 'UK', 18782.44),
(10, 'Peyton', 'Harris', 'USA', 158367.00);

SELECT
c.id,
CONCAT(first_name, ' ', last_name) AS name
FROM customer c
WHERE
LENGTH(first_name || last_name) < 12
ORDER BY
c.id ASC,
name ASC;



87. CREATE TABLE STUDENT (
    ID INTEGER PRIMARY KEY,
    NAME VARCHAR(100),
    SCORE FLOAT
);

INSERT INTO STUDENT (ID, NAME, SCORE) VALUES
(1, 'Bob', 58),
(2, 'John', 65.5),
(3, 'Harry', 45),
(4, 'Dick', 85),
(5, 'Dev', 85),
(6, 'Sid', 75),
(7, 'Tom', 98),
(8, 'Julia', 70.5),
(9, 'Erica', 81),
(10, 'Jerry', 81);

SELECT
    s.id,
    s.name
FROM 
    student s
WHERE
    s.score > (SELECT AVG(score) FROM student)
ORDER BY
    s.id;



88. CREATE TABLE ORDERS (
    ID INTEGER PRIMARY KEY,
    ORDER_DATE DATE,
    STATUS VARCHAR(50),
    CUSTOMER_ID INTEGER
);

INSERT INTO ORDERS (ID, ORDER_DATE, STATUS, CUSTOMER_ID) VALUES
(10100, '2003-01-06', 'PLACED', 363),
(10101, '2003-01-06', 'PLACED', 128),
(10102, '2003-01-06', 'IN TRANSIT', 181),
(10103, '2003-01-06', 'DELIVERED', 121),
(10104, '2003-01-07', 'DELIVERED', 114),
(10105, '2003-01-07', 'IN TRANSIT', 278),
(10106, '2003-01-07', 'PLACED', 114),
(10123, '2003-05-05', 'DELIVERED', 103);

SELECT
o.id,
o.order_date,
o.status,
o.customer_id
FROM orders o
WHERE
o.status != 'DELIVERED'
ORDER BY
o.order_date ASC
LIMIT 5;



89. CREATE TABLE CUSTOMER (
    ID INTEGER PRIMARY KEY,
    NAME VARCHAR(100),
    COUNTRY VARCHAR(100),
    CREDITS INTEGER
);

INSERT INTO CUSTOMER (ID, NAME, COUNTRY, CREDITS) VALUES
(1, 'Frances White', 'USA', 200350),
(2, 'Carolyn Bradley', 'UK', 15354),
(3, 'Annie Fernandez', 'France', 359200),
(4, 'Ruth Hanson', 'Albania', 1060),
(5, 'Paula Fuller', 'USA', 14789),
(6, 'Bonnie Johnston', 'China', 108243),
(7, 'Ruth Gutierrez', 'USA', 998999),
(8, 'Ernest Thomas', 'Canada', 505900),
(9, 'Joe Garza', 'UK', 18782),
(10, 'Anne Harris', 'USA', 158367);

SELECT 
c.id,
c.name
FROM
customer c
WHERE
c.country = 'USA' AND c.credits > 100000
ORDER BY
c.id ASC;


90. CREATE TABLE COLLECTION (
    RED INTEGER,
    GREEN INTEGER,
    BLUE INTEGER
);

INSERT INTO COLLECTION (RED, GREEN, BLUE) VALUES
(65, 65, 87),
(50, 50, 50),
(30, 50, 100),
(40, 90, 90),
(92, 50, 50);

SELECT 
CASE 
WHEN RED = GREEN AND GREEN = BLUE THEN 'GOOD'
WHEN RED = GREEN OR RED = BLUE OR GREEN = BLUE THEN 'BAD'
ELSE 
'WORSE'
END AS 
type_of_collection
FROM
collection;


91. CREATE TABLE COMPANY (
    ID INTEGER PRIMARY KEY,
    NAME VARCHAR(100)
);

CREATE TABLE SALARY (
    EMPLOYEE_ID INTEGER,
    COMPANY_ID INTEGER,
    SALARY INTEGER
);

INSERT INTO COMPANY (ID, NAME) VALUES
(1, 'Peoplesoft Inc'),
(2, 'Baker Hughes Incorporated'),
(3, 'MDU Resources Group Inc.'),
(4, 'DST Systems, Inc.'),
(5, 'Williams Companies Inc'),
(6, 'Fisher Scientific International Inc.'),
(7, 'Emcor Group Inc.'),
(8, 'Genuine Parts Company'),
(9, 'MPS Group Inc.'),
(10, 'Novellus Systems Inc');

INSERT INTO SALARY (EMPLOYEE_ID, COMPANY_ID, SALARY) VALUES
(2, 4, 27779),
(2, 9, 36330),
(3, 9, 71466),
(3, 10, 22804),
(5, 5, 48982),
(4, 4, 31493),
(6, 10, 26888),
(7, 3, 87118),
(7, 7, 70767),
(7, 9, 39929);

SELECT
c.name
FROM 
company c
JOIN 
salary s ON c.id = s.company_id
GROUP BY
c.name, 
c.id
HAVING 
AVG(s.salary) > 40000
ORDER BY
c.name;



92. CREATE TABLE DEPARTMENT (
    ID INTEGER PRIMARY KEY,
    NAME VARCHAR(100)
);

CREATE TABLE PROFESSOR (
    ID INTEGER PRIMARY KEY,
    NAME VARCHAR(100),
    DEPARTMENT_ID INTEGER,
    SALARY INTEGER,
    FOREIGN KEY (DEPARTMENT_ID) REFERENCES DEPARTMENT(ID)
);

CREATE TABLE COURSE (
    ID INTEGER PRIMARY KEY,
    NAME VARCHAR(100),
    DEPARTMENT_ID INTEGER,
    CREDITS INTEGER,
    FOREIGN KEY (DEPARTMENT_ID) REFERENCES DEPARTMENT(ID)
);

CREATE TABLE SCHEDULE (
    PROFESSOR_ID INTEGER,
    COURSE_ID INTEGER,
    SEMESTER VARCHAR(20),
    YEAR INTEGER,
    FOREIGN KEY (PROFESSOR_ID) REFERENCES PROFESSOR(ID),
    FOREIGN KEY (COURSE_ID) REFERENCES COURSE(ID)
);


INSERT INTO DEPARTMENT (ID, NAME) VALUES
(1, 'Mathematics'),
(2, 'Medical'),
(3, 'Physics'),
(4, 'Computer Science'),
(5, 'Geography'),
(6, 'Humanities & Social Sciences');

INSERT INTO PROFESSOR (ID, NAME, DEPARTMENT_ID, SALARY) VALUES
(1, 'Alex Daniels', 4, 7169),
(2, 'Drew Knight', 1, 9793),
(3, 'Jordan Myers', 4, 2514),
(4, 'Tyler Rodriguez', 3, 9606),
(5, 'Blake Gone', 5, 18647),
(6, 'Spencer George', 5, 18647),
(7, 'Ellis Vasquez', 4, 6353),
(8, 'Morgan Flores', 1, 25796),
(9, 'Riley Gilbert', 5, 35678),
(10, 'Peyton Stevens', 2, 26648);

INSERT INTO COURSE (ID, NAME, DEPARTMENT_ID, CREDITS) VALUES
(1, 'Pure Mathematics and Mathematical Statistics', 1, 3),
(2, 'Applied Mathematics and Theoretical Physics', 1, 5),
(3, 'Earth Science', 1, 7),
(4, 'Astronomy', 1, 6),
(5, 'Physics', 1, 8),
(6, 'Geography', 1, 7),
(7, 'Materials Science and Metallurgy', 1, 5),
(8, 'Chemistry', 1, 1),
(9, 'Clinical Biochemistry', 2, 3),
(10, 'Clinical Neuroscience', 2, 5);

INSERT INTO SCHEDULE (PROFESSOR_ID, COURSE_ID, SEMESTER, YEAR) VALUES
(4, 4, 3, 2003),
(3, 3, 1, 2011),
(1, 7, 5, 2011),
(7, 7, 1, 2010),
(4, 6, 1, 2001),
(9, 3, 1, 2012),
(10, 2, 4, 2009),
(1, 1, 3, 2009),
(1, 1, 3, 2014),
(1, 2, 3, 2008),
(1, 7, 5, 2007);

SELECT
p.name AS professor_name,
c.name AS course_name
FROM 
schedule s
JOIN 
professor p ON s.professor_id = p.id 
JOIN 
course c ON s.course_id = c.id
ORDER BY
p.name,
c.name;



93. CREATE TABLE PROFESSOR (
    ID INTEGER PRIMARY KEY,
    NAME VARCHAR(100),
    DEPARTMENT_ID INTEGER,
    SALARY INTEGER,
    FOREIGN KEY (DEPARTMENT_ID) REFERENCES DEPARTMENT(ID)
);


CREATE TABLE DEPARTMENT (
    ID INTEGER PRIMARY KEY,
    NAME VARCHAR(100)
);

CREATE TABLE COURSE (
    ID INTEGER PRIMARY KEY,
    NAME VARCHAR(100),
    DEPARTMENT_ID INTEGER,
    CREDITS INTEGER,
    FOREIGN KEY (DEPARTMENT_ID) REFERENCES DEPARTMENT(ID)
);


CREATE TABLE SCHEDULE (
    PROFESSOR_ID INTEGER,
    COURSE_ID INTEGER,
    SEMESTER INTEGER,
    YEAR INTEGER,
    FOREIGN KEY (PROFESSOR_ID) REFERENCES PROFESSOR(ID),
    FOREIGN KEY (COURSE_ID) REFERENCES COURSE(ID)
);


INSERT INTO PROFESSOR (ID, NAME, DEPARTMENT_ID, SALARY) VALUES
(1, 'Alex Burton', 5, 7340),
(2, 'Tyler Matthews', 2, 14521),
(3, 'Spencer Peters', 1, 10487),
(4, 'Ellis Marshall', 3, 6133),
(5, 'Peyton Fields', 5, 26648),
(6, 'Riley Peterson', 1, 35678),
(7, 'Morgan Lee', 2, 25796),
(8, 'Jordan Diaz', 1, 17221),
(9, 'Drew Hicks', 5, 16613),
(10, 'Blake Foster', 4, 28526);

INSERT INTO DEPARTMENT (ID, NAME) VALUES
(1, 'Physical Sciences'),
(2, 'Clinical Medicine'),
(3, 'Biological Sciences'),
(4, 'Arts and Humanities'),
(5, 'Technology'),
(6, 'Humanities & Social Sciences');


INSERT INTO COURSE (ID, NAME, DEPARTMENT_ID, CREDITS) VALUES
(1, 'Pure Mathematics and Mathematical Statistics', 1, 3),
(2, 'Applied Mathematics and Theoretical Physics', 1, 5),
(3, 'Earth Science', 1, 7),
(4, 'Astronomy', 1, 6),
(5, 'Physics', 1, 8),
(6, 'Geography', 1, 7),
(7, 'Materials Science and Metallurgy', 1, 5),
(8, 'Chemistry', 1, 1),
(9, 'Clinical Biochemistry', 2, 3),
(10, 'Clinical Neuroscience', 2, 5);

INSERT INTO SCHEDULE (PROFESSOR_ID, COURSE_ID, SEMESTER, YEAR) VALUES
(5, 3, 6, 2012),
(7, 3, 1, 2013),
(5, 7, 6, 2010),
(2, 10, 2, 2004),
(5, 1, 1, 2011),
(2, 9, 4, 2005),
(7, 10, 6, 2009),
(5, 6, 4, 2007),
(7, 9, 1, 2014),
(9, 9, 5, 2011);

SELECT
p.name,
c.name
FROM 
professor p
JOIN schedule s ON p.id = s.professor_id
JOIN course c ON c.id = s.course_id
GROUP BY
p.name,
c.name
ORDER BY
p.name,
c.name;



94. CREATE TABLE DEPARTMENT (
    ID INT PRIMARY KEY,
    NAME VARCHAR(100)
);

CREATE TABLE PROFESSOR (
    ID INT PRIMARY KEY,
    NAME VARCHAR(100),
    DEPARTMENT_ID INT,
    SALARY INT,
    FOREIGN KEY (DEPARTMENT_ID) REFERENCES DEPARTMENT(ID)
);

INSERT INTO DEPARTMENT (ID, NAME) VALUES
(1, 'Biological Sciences'),
(2, 'Technology'),
(3, 'Humanities & Social Sciences'),
(4, 'Clinical Medicine'),
(5, 'Arts & Humanities'),
(6, 'Physical Sciences');

INSERT INTO PROFESSOR (ID, NAME, DEPARTMENT_ID, SALARY) VALUES
(1, 'Shaun Rivera', 1, 22606),
(8, 'Ruth Price', 3, 9287),
(9, 'Julie Gonzalez', 4, 18870),
(2, 'Craig Elliott', 5, 27524),
(10, 'Scott Butler', 1, 26200),
(3, 'Nancy Russell', 2, 7076),
(4, 'Carlson Johnson', 1, 7249),
(7, 'Louis Schmidt', 1, 13437),
(5, 'Terri Thompson', 3, 28432),
(6, 'Keith Gilbert', 5, 12610);

SELECT 
p.name,
p.salary
FROM 
professor p
JOIN 
department d ON p.department_id = d.id
WHERE
d.name != 'Arts & Humanities'
AND 
p.salary > (
SELECT 
MIN(p2.salary)
FROM 
professor p2
JOIN 
department d2 ON p2.department_id = d2.id
WHERE
d2.name = 'Arts & Humanities'
);



95. CREATE TABLE STUDENTS (
    STUDENT_ID INT PRIMARY KEY,
    STUDENT_NAME VARCHAR(100),
    STUDENT_AGE INT
);

CREATE TABLE MAJORS (
    MAJOR_ID INT PRIMARY KEY,
    MAJOR_NAME VARCHAR(100)
);

CREATE TABLE REGISTER (
    STUDENT_ID INT,
    MAJOR_ID INT,
    FOREIGN KEY (STUDENT_ID) REFERENCES STUDENTS(STUDENT_ID),
    FOREIGN KEY (MAJOR_ID) REFERENCES MAJORS(MAJOR_ID)
);

INSERT INTO STUDENTS (STUDENT_ID, STUDENT_NAME, STUDENT_AGE) VALUES
(1, 'John', 20),
(2, 'Masie', 21),
(3, 'Harry', 21);

INSERT INTO MAJORS (MAJOR_ID, MAJOR_NAME) VALUES
(1000, 'Computer Science'),
(2000, 'Biology'),
(3000, 'Physics');

INSERT INTO REGISTER (STUDENT_ID, MAJOR_ID) VALUES
(2, 1000),
(3, 3000),
(1, 2000);


SELECT
s.student_name,
m.major_name
FROM 
students s
JOIN 
register r ON s.student_id = r.student_id
JOIN
majors m ON r.major_id = m.major_id
GROUP BY
s.student_id,
m.major_name
ORDER BY
s.student_id
LIMIT 20;



96. CREATE TABLE STUDENT (
    ID INT PRIMARY KEY,
    AGE INT,
    SCORE INT
);

INSERT INTO STUDENT (ID, AGE, SCORE) VALUES
(1, 19, 91),
(2, 20, 90),
(3, 20, 87),
(4, 21, 72),
(5, 19, 98),
(6, 20, 50);

SELECT 
s.score
FROM
student s
ORDER BY
s.score DESC
LIMIT 1 OFFSET 212;


97. CREATE TABLE EMPLOYEE (
    NAME VARCHAR(100),
    PHONE VARCHAR(15),
    AGE INT
);

INSERT INTO EMPLOYEE (NAME, PHONE, AGE) VALUES
('Sam', '1000040000', 30),
('Alex', '1000020000', 60),
('Alex', '1000020012', 65),
('Sam', '1000040000', 30),
('Chris', '1000012000', 34),
('Chris', '1000012000', 34);

SELECT
e.name
FROM 
employee e
GROUP BY
e.name,
e.phone,
e.age
HAVING
COUNT(*) > 1;


98. CREATE TABLE transactions (
    name VARCHAR(50),
    customer VARCHAR(50),
    dt TIMESTAMP,
    debit DECIMAL(8,2),
    credit DECIMAL(8,2)
);

INSERT INTO transactions (dt, customer, debit, credit) VALUES
('2021-11-30 12:48:22', 'Arney Cuff', 6.43, 16.12),
('2021-12-25 19:00:46', 'Arney Cuff', 97.78, 12.53),
('2021-11-27 21:34:24', 'Donaugh Furneaux', 89.71, 85.04),
('2021-11-25 07:31:37', 'Ferrell Brunn', 63.58, 28.58),
('2021-11-25 15:30:56', 'Gibbie Jurisic', 25.81, 13.75),
('2021-11-21 00:09:50', 'Harley Lyddiard', 57.49, 7.11),
('2021-12-02 13:08:52', 'Harley Lyddiard', 48.33, 82.35),
('2021-11-24 03:51:13', 'Kippy Jelly', 50.34, 12.91),
('2021-12-04 10:11:40', 'Latrina Jackman', 10.73, 39.51),
('2021-12-12 13:02:50', 'Latrina Jackman', 5.35, 96.74),
('2021-12-20 17:31:44', 'Latrina Jackman', 54.99, 92.73),
('2021-12-30 13:40:43', 'Maribel Braim', 57.06, 21.37),
('2021-12-02 09:57:35', 'Orrin Curley', 65.44,  51.31),
('2021-12-14 19:57:25', 'Orrin Curley', 40.04,  96.44),
('2021-11-20 09:28:11', 'Rasla Venny', 80.33,  20.69),
('2021-12-08 03:31:31', 'Rasla Venny', 55.43,  99.04),
('2021-12-09 21:31:29', 'Rasla Venny',  87.96,  5.87),
('2021-12-11 13:02:54', 'Rasla Venny',  45.42,  55.81),
('2021-12-28 18:04:52', 'Rasla Venny',  68.17,  85.30);

SELECT
t.customer,
ROUND(SUM(t.debit - t.credit), 2) AS balance
FROM 
transactions t
WHERE
t.dt BETWEEN '2021-12-1' AND '2021-12-31'
GROUP BY
t.customer
ORDER BY
t.customer ASC;





 
