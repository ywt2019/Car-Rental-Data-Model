# car-rental-data-model
an example car rental company’s data model in Oracle Relational Database

PART II - A
Script #1 - Table Creation
DROP TABLE EMPLOYEE CASCADE CONSTRAINTS;
DROP TABLE FAULTREPORT;
DROP TABLE RAGREEMENT;
DROP TABLE VEHICLE;
DROP TABLE OUTLET;
DROP TABLE CLIENT;

CREATE TABLE Client
(ClientNo    NUMBER(4),
 FName       VARCHAR2(20),
 LName       VARCHAR2(20),
 Phone       VARCHAR2(15),
 Email       VARCHAR2(30),
 WebAddress  VARCHAR2(30),
 Street      VARCHAR2(25),
 City        VARCHAR2(25),
 State       CHAR(2),
 ZipCode     CHAR(5),
 CONSTRAINT  Client_ClientNo_PK       PRIMARY KEY (ClientNo),
 CONSTRAINT  Client_Email_CK          CHECK       (REGEXP_LIKE(Email, '^[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+.[a-zA-Z0-9-.]+$')),
 CONSTRAINT  Client_WebAddress_CK     CHECK       (REGEXP_LIKE(WebAddress, '^(http:\/\/)?[a-zA-Z0-9_\-]+\.[a-zA-Z0-9_\-]+\.[a-zA-Z0-9_\-]+$'))
);

CREATE TABLE EMPLOYEE
(EmpNo       NUMBER(4),
 FName       VARCHAR2(10),
 LName       VARCHAR2(10),
 Position    VARCHAR2(30),
 Phone       VARCHAR2(12),
 Email       VARCHAR2(30),
 DOB         DATE DEFAULT SYSDATE,
 Gender      VARCHAR2(6),
 Salary      NUMBER(12, 2),
 HireDate    DATE DEFAULT SYSDATE,
 OutNo       NUMBER(4),
 Super_No    NUMBER(4),
 CONSTRAINT  Employee_EmpNo_PK      PRIMARY KEY (EmpNo),
 CONSTRAINT  Employee_Salary_CK     CHECK (Salary > 0),
 CONSTRAINT  Employee_Email_CK      CHECK (REGEXP_LIKE(Email, '[A-Za-z|0-9]{1,}@employee.carrental.com')),
 CONSTRAINT  Employee_Super_No_FK   FOREIGN KEY (Super_No)     REFERENCES EMPLOYEE (EmpNo)
);

CREATE TABLE OUTLET
(OutNo        NUMBER(4),
 Street       VARCHAR2(20),
 City         VARCHAR2(20),
 State        VARCHAR2(2),
 Zipcode      VARCHAR2(5),
 Phone        VARCHAR2(12),
 ManagerNo    NUMBER(4),
 CONSTRAINT   Outlet_OutNo_PK       PRIMARY KEY (OutNo),
 CONSTRAINT   Outlet_ManagerNo_FK   FOREIGN KEY (ManagerNo)     REFERENCES EMPLOYEE (EmpNo)
 );

CREATE TABLE Vehicle
(LicenseNo       VARCHAR2(7),
 Make            VARCHAR2(15),
 Model           VARCHAR2(20),
 Color           VARCHAR2(20),
 Year            NUMBER(4),
 NoDoors         NUMBER(1),
 Capacity        NUMBER(6,2),
 DailyRate       NUMBER(8,2),
 InspectionDate  DATE DEFAULT SYSDATE,
 OutNo           NUMBER(4),
 CONSTRAINT      Vehicle_LicenseNo_PK   PRIMARY KEY (LicenseNo),
 CONSTRAINT      Vehicle_OutNo_FK       FOREIGN KEY (OutNo) REFERENCES Outlet (outNo),
 CONSTRAINT      Vehicle_NoDoors_CK     CHECK       (NoDoors IN (2, 4)),
 CONSTRAINT      Vehicle_Capacity_CK    CHECK       (Capacity > 0),
 CONSTRAINT      Vehicle_DailyRate_CK   CHECK       (DailyRate > 0)
);

CREATE TABLE RAgreement
(RentalNo       NUMBER(4),
 StartDate      DATE DEFAULT SYSDATE,
 ReturnDate     DATE DEFAULT SYSDATE,
 MileageBefore  NUMBER(10),
 MileageAfter   NUMBER(10),
 InsuranceType  VARCHAR2(30),
 ClientNo       NUMBER(4),
 LicenseNo      VARCHAR2(7),
 CONSTRAINT     RAgreement_RentalNo_PK       PRIMARY KEY (RentalNo),
 CONSTRAINT     RAgreement_ClientNo_FK       FOREIGN KEY (ClientNo)  REFERENCES Client  (ClientNo),
 CONSTRAINT     RAgreement_LicenseNo_FK      FOREIGN KEY (LicenseNo) REFERENCES Vehicle (LicenseNo),
 CONSTRAINT     RAgreement_ReturnDate_CK     CHECK       (ReturnDate > StartDate),
 CONSTRAINT     RAgreement_MileageBefore_CK  CHECK       (MileageBefore >= 0),
 CONSTRAINT     RAgreement_MileageAfter_CK   CHECK       (MileageAfter >= MileageBefore)
);

CREATE TABLE FAULTREPORT
(ReportNum      NUMBER(4),
 DateChecked    DATE DEFAULT SYSDATE,
 Comments       VARCHAR2(100),
 EmpNo          NUMBER(4),
 LicenseNo      VARCHAR2(7),
 RentalNo       NUMBER(4),
 CONSTRAINT     FaultReport_ReportNum_PK      PRIMARY KEY (ReportNum),
 CONSTRAINT     FaultReport_EmpNo_FK          FOREIGN KEY (EmpNo)         REFERENCES EMPLOYEE (EmpNo),
 CONSTRAINT     FaultReport_LicenseNo_FK      FOREIGN KEY (LicenseNo)     REFERENCES VEHICLE (LicenseNo),
 CONSTRAINT     FaultReport_RentalNo_FK       FOREIGN KEY (RentalNo)      REFERENCES RAGREEMENT (RentalNo)
 );

ALTER TABLE EMPLOYEE
ADD CONSTRAINT  Employee_OutNo_FK      FOREIGN KEY (OutNo)        REFERENCES OUTLET (outNo);


Script #2 - Insertions
--< OUTLET table inserts >--
INSERT INTO OUTLET VALUES
(1, 'Cottages',         'Corpus Christi', 'TX', '73301', '805-250-4174', NULL);

INSERT INTO OUTLET VALUES
(2, 'Stable Wynd',      'Anaheim',        'PA', '15417', '518-952-9887', NULL);

INSERT INTO OUTLET VALUES
(3, 'Mountfield Rd',    'Fort Wayne',     'IL', '46201', '443-760-6920', NULL);

INSERT INTO OUTLET VALUES
(4, 'Brooks Boulevard', 'Seatlle',        'WA', '20001', '859-359-5243', NULL);


--< EMPLOYEE table inserts >--
INSERT INTO EMPLOYEE VALUES
(5, 'Sumaiya',  'Cannon',    'Mechanics',                  '215-778-8760', 'sc@employee.carrental.com', '28-JUL-1987', 'Female', 100000, '03-JUNE-2005', 1, NULL);

INSERT INTO EMPLOYEE VALUES
(3, 'Reid',     'Grimes',    'Administrative Assistants',  '541-925-7463', 'rg@employee.carrental.com', '16-MAY-1979', 'Female', 100000, '13-JUNE-2005', 2, NULL);

INSERT INTO EMPLOYEE VALUES
(9, 'Tabitha',  'Vaughn',    'Sales Reps',                 '607-723-7648', 'tv@employee.carrental.com', '30-NOV-1983', 'Female', 100000, '10-APR-2005',  3, NULL);

INSERT INTO EMPLOYEE VALUES
(2, 'Raja',     'Byers',     'Mechanics',                  '604-855-4380', 'rb@employee.carrental.com', '01-APR-1982', 'Male',   45000, '23-MAY-2017',   4, 5);

INSERT INTO EMPLOYEE VALUES
(14, 'Ted',     'Payne',     'Sales Reps',                 '219-261-8401', 'tp@employee.carrental.com', '11-APR-1991', 'Female', 45000, '11-JUN-2017',   3, 9);

INSERT INTO EMPLOYEE VALUES
(11, 'Tracey',  'Elliott',   'Administrative Assistants',  '559-488-9720', 'tel@employee.carrental.com', '19-JAN-1995', 'Female', 45000, '04-FEB-2017',  2, 3);

INSERT INTO EMPLOYEE VALUES
(1, 'Leja',     'Mckee',     'Sales Reps',                 '342-543-2454', 'lm@employee.carrental.com', '24-MAR-1988', 'Male',    32000, '01-APR-2020',  1, 14);

INSERT INTO EMPLOYEE VALUES
(4, 'Farrah',    'Adams',     'Mechanics',                 '580-920-4896', 'fa@employee.carrental.com', '17-JUN-1989', 'Male',    32000, '17-APR-2020',  2, 2);

INSERT INTO EMPLOYEE VALUES
(6, 'Karen',     'Greene',    'Administrative Assistants', '651-451-1318', 'kg@employee.carrental.com', '04-AUG-1995', 'Female',  32000, '09-JAN-2020',  3, 11);

INSERT INTO EMPLOYEE VALUES
(7, 'Emma',      'Lawrence',  'Sales Reps',                '419-971-4305', 'el@employee.carrental.com', '08-SEP-1991', 'Male',    32000, '19-FEB-2020',  4, 14);

INSERT INTO EMPLOYEE VALUES
(8, 'Tommy',     'Burke',     'Mechanics',                 '302-447-4734', 'tb@employee.carrental.com', '12-OCT-1992', 'Female',  32000, '28-NOV-2020',  3, 2);

INSERT INTO EMPLOYEE VALUES
(10, 'Tara',     'Rodriquez', 'Sales Reps',                '315-778-2453', 'tr@employee.carrental.com', '21-DEC-1994', 'Male',    32000, '01-DEC-2020',  2, 14);

INSERT INTO EMPLOYEE VALUES
(12, 'Troy',     'Edwards',   'Administrative Assistants', '916-844-2665', 'ted@employee.carrental.com', '14-FEB-1996', 'Male',   32000, '08-MAR-2020',  1, 11);

INSERT INTO EMPLOYEE VALUES
(13, 'Jan',      'Mitchell',  'Mechanics',                 '516-894-7506', 'jm@employee.carrental.com', '10-MAR-1997', 'Female',  32000, '14-JUL-2020',  2, 2);

INSERT INTO EMPLOYEE VALUES
(15, 'Cliggord', 'Frazier',   'Sales Reps',                '516-897-7506', 'cf@employee.carrental.com', '06-MAY-1992', 'Male',    32000, '12-DEC-2020',  3, 14);


--< update statements for outlet >--
UPDATE OUTLET
SET ManagerNo = 5
WHERE OutNo = 1;

UPDATE OUTLET
SET ManagerNo = 3
WHERE OutNo = 2;

UPDATE OUTLET
SET ManagerNo = 9
WHERE OutNo = 3;

UPDATE OUTLET
SET ManagerNo = 2
WHERE OutNo = 4;


--< VEHICLE table inserts >--
INSERT INTO Vehicle VALUES 
('ABC1234', 'Mercedes-Benz', 'SL65 AMG',   'Black',  2009, 4, 800,  392.5, TO_DATE('03-SEP-2020', 'DD-MON-YYYY'), 1);

INSERT INTO Vehicle VALUES 
('MNO7281', 'Audi',          'A3',         'Black',  2013, 4, 1500, 487.5, TO_DATE('23-APR-2022', 'DD-MON-YYYY'), 4);

INSERT INTO Vehicle VALUES 
('NYO8364', 'Audi',          'R8',         'White',  2015, 2, 1500, 512.5, TO_DATE('19-MAY-2021', 'DD-MON-YYYY'), 1);

INSERT INTO Vehicle VALUES 
('PMB7810', 'Hyundai',       'Accent',     'Black',  2016, 4, 900,  546.5, TO_DATE('06-MAR-2022', 'DD-MON-YYYY'), 2);

INSERT INTO Vehicle VALUES 
('PMO4452', 'Hyundai',       'Elantra',    'Gray',   2016, 4, 1500, 569.5, TO_DATE('17-SEP-2020', 'DD-MON-YYYY'), 2);

INSERT INTO Vehicle VALUES 
('RES9829', 'Hyundai',       'Azera',      'White',  2017, 4, 1400, 578.5, TO_DATE('18-OCT-2021', 'DD-MON-YYYY'), 3);

INSERT INTO Vehicle VALUES 
('BAT9822', 'Audi',          'R8',         'Gray',   2015, 4, 1500, 512.5, TO_DATE('19-JAN-2022', 'DD-MON-YYYY'), 2);

INSERT INTO Vehicle VALUES 
('BJK6371', 'Mercedes-Benz', 'AMG CLS 53', 'Black',  2020, 2, 850,  882.5, TO_DATE('01-DEC-2021', 'DD-MON-YYYY'), 3);

INSERT INTO Vehicle VALUES 
('XYZ4526', 'Toyota',        'Avalon',     'Silver', 2021, 4, 1200, 906.5, TO_DATE('15-NOV-2020', 'DD-MON-YYYY'), 4);


--< CLIENT table inserts >--
INSERT INTO Client VALUES 
(1011, 'Florencio', 'Barton',      '555-566-6313', 'fbarton@alliedcorp.org',     'www.alliedcorp.org',       '8425 North Ave.',        'Philadelphia',         'PA', '30213');

INSERT INTO Client VALUES
(1012, 'Lenny',     'Abramowski',  '555-637-7282', 'labramowski@mainquartz.org', 'www.mainquartz.org',       '56 South Newcastle Dr.', 'Tacoma',               'WA', '02125');

INSERT INTO Client VALUES
(1013, 'Zach',      'Demerritt',   '555-426-8792', 'zachd@greensworthgroup.org', 'www.greensworthgroup.org', '8895 Bay Meadows Ave.',  'Lemont',               'IL', '60439');

INSERT INTO Client VALUES
(1014, 'Erin',      'Gonzalez',    '555-436-7639', 'egonzalez@projectgr.gov',    'www.projectgr.gov',        '851 Gregory Ave.',       'Park Ridge',           'IL', '60068');

INSERT INTO Client VALUES
(1015, 'Evelyn',    'Chen',        '555-637-4583', 'cevelyn@lansinghigh.edu',    'www.lansinghigh.edu',      '8 Foster Ave.',          'Monroeville',          'PA', '48910');

INSERT INTO Client VALUES
(1016, 'Robbie',    'Basse',       '555-783-0828', 'rbasse@canyonview.edu',      'www.canyonview.edu',       '82 Mechanic Avenue',     'Lake Villa',           'IL', '60046');

INSERT INTO Client VALUES 
(1017, 'Addison',   'Mazzetta',    '555-468-9837', 'addisonm@milleniumllc.com',  'www.milleniumllc.com',     '237 Prospect Dr.',       'Dallas',               'TX', '29577');

INSERT INTO Client VALUES
(1018, 'Lucy',      'Descheneaux', '555-898-5435', 'lucyd@pellelectric.com',     'www.pellelectric.com',     '9652 Cambridge Lane',    'Avon Lake',            'OH', '44012');

INSERT INTO Client VALUES
(1019, 'Rosa',      'Santiago',    '555-083-7972', 's.rosa@capitalgrid.net',     'www.capitalgrid.net',      '731 St Louis Ave.',      'Feasterville Trevose', 'PA', '19053');

INSERT INTO Client VALUES
(1020, 'Beatrice',  'Ginsburg',    '555-537-9837', 'bginsburg@nextgen.io',       'www.nextgen.io',           '811 Sierra St.',         'Vernon Hills',         'IL', '60061');

INSERT INTO Client VALUES
(1021, 'Wilbert',   'Richner',     '555-974-7942', 'wrichner1@outlook.com',      NULL,                       '38 Amherst St.',         'Pottstown',            'PA', '19464');

INSERT INTO Client VALUES
(1022, 'Jacob',     'Wu',          '555-573-5375', 'jacob.wu@gmail.com',         NULL,                       '398 St Paul St.',        'Plano',                'TX', '29063');


--< RAGGREEMENT table inserts >--
INSERT INTO RAgreement VALUES
(2022, TO_DATE('13-JUN-2022 13:41', 'DD-MON-YYYY HH24:MI'), TO_DATE('15-JUL-2022 14:23', 'DD-MON-YYYY HH24:MI'), 12341,  13456,  'Liability Coverage',         1012, 'XYZ4526');

INSERT INTO RAgreement VALUES
(2023, TO_DATE('14-JUN-2022 10:13', 'DD-MON-YYYY HH24:MI'), TO_DATE('21-JUN-2022 15:47', 'DD-MON-YYYY HH24:MI'), 16786,  17864,  'Collision Coverage',         1011, 'BJK6371');

INSERT INTO RAgreement VALUES
(2024, TO_DATE('23-JUN-2022 12:39', 'DD-MON-YYYY HH24:MI'), TO_DATE('30-SEP-2022 14:29', 'DD-MON-YYYY HH24:MI'), 156787, 196789, 'Collision Coverage',         1015, 'ABC1234');

INSERT INTO RAgreement VALUES
(2025, TO_DATE('01-JUL-2022 11:01', 'DD-MON-YYYY HH24:MI'), TO_DATE('15-JUL-2022 13:16', 'DD-MON-YYYY HH24:MI'), 135268, 136023, 'Comprehensive Coverage',     1016, 'MNO7281');

INSERT INTO RAgreement VALUES
(2026, TO_DATE('08-JUL-2022 15:15', 'DD-MON-YYYY HH24:MI'), TO_DATE('16-JUL-2022 15:11', 'DD-MON-YYYY HH24:MI'), 17865,  18356,  'Liability Coverage',         1022, 'BJK6371');

INSERT INTO RAgreement VALUES
(2027, TO_DATE('17-JUL-2022 16:11', 'DD-MON-YYYY HH24:MI'), TO_DATE('01-AUG-2022 12:07', 'DD-MON-YYYY HH24:MI'), 90352,  91567,  'Comprehensive Coverage',     1017, 'PMO4452');

INSERT INTO RAgreement VALUES
(2028, TO_DATE('30-JUL-2022 09:19', 'DD-MON-YYYY HH24:MI'), TO_DATE('13-AUG-2022 16:03', 'DD-MON-YYYY HH24:MI'), 18356,  19125,  'Collision Coverage',         1015, 'BAT9822');

INSERT INTO RAgreement VALUES
(2029, TO_DATE('05-AUG-2022 16:03', 'DD-MON-YYYY HH24:MI'), TO_DATE('30-SEP-2022 15:51', 'DD-MON-YYYY HH24:MI'), 13456,  15113,  'Liability Coverage',         1012, 'XYZ4526');

INSERT INTO RAgreement VALUES
(2030, TO_DATE('12-AUG-2022 12:19', 'DD-MON-YYYY HH24:MI'), TO_DATE('31-AUG-2022 14:40', 'DD-MON-YYYY HH24:MI'), 91567,  92078,  'Personal Injury Protection', 1011, 'PMO4452');

INSERT INTO RAgreement VALUES
(2031, TO_DATE('19-AUG-2022 14:23', 'DD-MON-YYYY HH24:MI'), TO_DATE('01-SEP-2022 15:38', 'DD-MON-YYYY HH24:MI'), 92789,  93452,  'Collision Coverage',         1014, 'NYO8364');

INSERT INTO RAgreement VALUES
(2032, TO_DATE('03-SEP-2022 13:08', 'DD-MON-YYYY HH24:MI'), TO_DATE('03-OCT-2022 11:44', 'DD-MON-YYYY HH24:MI'), 19125,  21065,  'Personal Injury Protection', 1020, 'BJK6371');

INSERT INTO RAgreement VALUES
(2033, TO_DATE('18-SEP-2022 10:58', 'DD-MON-YYYY HH24:MI'), TO_DATE('24-SEP-2022 10:58', 'DD-MON-YYYY HH24:MI'), 93452,  93999,  'Collision Coverage',         1011, 'NYO8364');

INSERT INTO RAgreement VALUES
(2034, TO_DATE('20-SEP-2022 11:44', 'DD-MON-YYYY HH24:MI'), TO_DATE('12-OCT-2022 13:08', 'DD-MON-YYYY HH24:MI'), 82683,  83902,  'Liability Coverage',         1014, 'RES9829');

INSERT INTO RAgreement VALUES
(2035, TO_DATE('25-SEP-2022 15:38', 'DD-MON-YYYY HH24:MI'), TO_DATE('23-NOV-2022 12:19', 'DD-MON-YYYY HH24:MI'), 92078,  94003,  'Comprehensive Coverage',     1019, 'PMO4452');

INSERT INTO RAgreement VALUES
(2036, TO_DATE('29-SEP-2022 14:40', 'DD-MON-YYYY HH24:MI'), TO_DATE('05-NOV-2022 16:03', 'DD-MON-YYYY HH24:MI'), 93999,  95123,  'Collision Coverage',         1018, 'NYO8364');

INSERT INTO RAgreement VALUES
(2037, TO_DATE('15-OCT-2022 15:51', 'DD-MON-YYYY HH24:MI'), TO_DATE('29-OCT-2022 09:19', 'DD-MON-YYYY HH24:MI'), 196789, 197128, 'Liability Coverage',         1022, 'ABC1234');

INSERT INTO RAgreement VALUES
(2038, TO_DATE('17-OCT-2022 16:03', 'DD-MON-YYYY HH24:MI'), TO_DATE('03-NOV-2022 16:11', 'DD-MON-YYYY HH24:MI'), 83903,  85235,  'Collision Coverage',         1021, 'RES9829');

INSERT INTO RAgreement VALUES
(2039, TO_DATE('17-OCT-2022 12:07', 'DD-MON-YYYY HH24:MI'), TO_DATE('21-NOV-2022 15:15', 'DD-MON-YYYY HH24:MI'), 21065,  23104,  'Liability Coverage',         1013, 'BJK6371');

INSERT INTO RAgreement VALUES
(2040, TO_DATE('01-NOV-2022 15:11', 'DD-MON-YYYY HH24:MI'), TO_DATE('15-NOV-2022 11:01', 'DD-MON-YYYY HH24:MI'), 197128, 197467, 'Comprehensive Coverage',     1022, 'ABC1234');

INSERT INTO RAgreement VALUES
(2041, TO_DATE('08-NOV-2022 13:16', 'DD-MON-YYYY HH24:MI'), TO_DATE('25-NOV-2022 12:39', 'DD-MON-YYYY HH24:MI'), 85235,  87689,  'Collision Coverage',         1013, 'RES9829');

INSERT INTO RAgreement VALUES
(2042, TO_DATE('11-NOV-2022 14:29', 'DD-MON-YYYY HH24:MI'), TO_DATE('22-NOV-2022 10:13', 'DD-MON-YYYY HH24:MI'), 95123,  96034,  'Liability Coverage',         1017, 'NYO8364');

INSERT INTO RAgreement VALUES
(2043, TO_DATE('13-NOV-2022 11:27', 'DD-MON-YYYY HH24:MI'), TO_DATE('25-NOV-2022 16:19', 'DD-MON-YYYY HH24:MI'), 90563,  93173,  'Comprehensive Coverage',     1019, 'PMB7810');

INSERT INTO RAgreement VALUES
(2044, TO_DATE('22-NOV-2022 15:47', 'DD-MON-YYYY HH24:MI'), NULL,                                                23104,  NULL,   'Collision Coverage',         1015, 'BJK6371');


--< FAULTREPORT table inserts >--
INSERT INTO FAULTREPORT VALUES
(1, TO_DATE('16-JUL-2022 16:29', 'DD-MON-YYYY HH24:MI'), 'Flat Tire',                     8,  'BJK6371', 2026);

INSERT INTO FAULTREPORT VALUES
(2, TO_DATE('22-JUN-2022 09:20', 'DD-MON-YYYY HH24:MI'), 'Scratch on the left side',      13, 'BJK6371', 2023);

INSERT INTO FAULTREPORT VALUES
(3, TO_DATE('15-JUL-2022 16:27', 'DD-MON-YYYY HH24:MI'), 'Front Windshield Wiper Broken', 4,  'XYZ4526', 2022);

INSERT INTO FAULTREPORT VALUES
(4, TO_DATE('14-AUG-2022 09:03', 'DD-MON-YYYY HH24:MI'), 'Cracked side view mirror',      2,  'BAT9822', 2028);

INSERT INTO FAULTREPORT VALUES
(5, TO_DATE('31-AUG-2022 16:40', 'DD-MON-YYYY HH24:MI'), 'Malfunctioning brake',          5,  'PMO4452', 2030);

INSERT INTO FAULTREPORT VALUES
(6, TO_DATE('03-OCT-2022 15:24', 'DD-MON-YYYY HH24:MI'), 'Broken headlights',             8,  'BJK6371', 2032);

INSERT INTO FAULTREPORT VALUES
(7, TO_DATE('12-OCT-2022 16:18', 'DD-MON-YYYY HH24:MI'), 'Scratched side view mirror',    13, 'RES9829', 2034);

INSERT INTO FAULTREPORT VALUES
(8, TO_DATE('01-AUG-2022 17:07', 'DD-MON-YYYY HH24:MI'), 'Scratch on the right side',     4,  'PMO4452', 2027);

INSERT INTO FAULTREPORT VALUES
(9, TO_DATE('23-NOV-2022 15:39', 'DD-MON-YYYY HH24:MI'), 'Carplay not working',           8,  'PMO4452', 2035);

INSERT INTO FAULTREPORT VALUES
(10, TO_DATE('30-SEP-2022 16:58', 'DD-MON-YYYY HH24:MI'), 'High Beam broken',             2,  'XYZ4526', 2029);

INSERT INTO FAULTREPORT VALUES
(11, TO_DATE('29-OCT-2022 12:56', 'DD-MON-YYYY HH24:MI'), 'Cracked side window',          5,  'ABC1234', 2037);

INSERT INTO FAULTREPORT VALUES
(12, TO_DATE('25-NOV-2022 17:39', 'DD-MON-YYYY HH24:MI'), 'Cracked Front Windshield',     8,  'RES9829', 2041);

COMMIT;

#3 - Display Table Contents
SQL> SELECT EmpNo, FName, LName, Position, Phone, Email, DOB, Gender,
  2         TO_CHAR(Salary, '$999,990.99') Salary, HireDate, OutNo,
  3         NVL(TO_CHAR(Super_No), ' --- ') Super_No
  4    FROM Employee;

     EMPNO FNAME           LNAME      POSITION                       PHONE        EMAIL                          DOB       GENDER SALARY       HIREDATE       OUTNO SUPER
---------- --------------- ---------- ------------------------------ ------------ ------------------------------ --------- ------ ------------ --------- ---------- -----
         5 Sumaiya         Cannon     Mechanics                      215-778-8760 sc@employee.carrental.com      28-JUL-87 Female  $100,000.00 03-JUN-05          1  ---
         3 Reid            Grimes     Administrative Assistants      541-925-7463 rg@employee.carrental.com      16-MAY-79 Female  $100,000.00 13-JUN-05          2  ---
         9 Tabitha         Vaughn     Sales Reps                     607-723-7648 tv@employee.carrental.com      30-NOV-83 Female  $100,000.00 10-APR-05          3  ---
         2 Raja            Byers      Mechanics                      604-855-4380 rb@employee.carrental.com      01-APR-82 Male     $45,000.00 23-MAY-17          4 5
        14 Ted             Payne      Sales Reps                     219-261-8401 tp@employee.carrental.com      11-APR-91 Female   $45,000.00 11-JUN-17          3 9
        11 Tracey          Elliott    Administrative Assistants      559-488-9720 tel@employee.carrental.com     19-JAN-95 Female   $45,000.00 04-FEB-17          2 3
         1 Leja            Mckee      Sales Reps                     342-543-2454 lm@employee.carrental.com      24-MAR-88 Male     $32,000.00 01-APR-20          1 14
         4 Farrah          Adams      Mechanics                      580-920-4896 fa@employee.carrental.com      17-JUN-89 Male     $32,000.00 17-APR-20          2 2
         6 Karen           Greene     Administrative Assistants      651-451-1318 kg@employee.carrental.com      04-AUG-95 Female   $32,000.00 09-JAN-20          3 11
         7 Emma            Lawrence   Sales Reps                     419-971-4305 el@employee.carrental.com      08-SEP-91 Male     $32,000.00 19-FEB-20          4 14
         8 Tommy           Burke      Mechanics                      302-447-4734 tb@employee.carrental.com      12-OCT-92 Female   $32,000.00 28-NOV-20          3 2
        10 Tara            Rodriquez  Sales Reps                     315-778-2453 tr@employee.carrental.com      21-DEC-94 Male     $32,000.00 01-DEC-20          2 14
        12 Troy            Edwards    Administrative Assistants      916-844-2665 ted@employee.carrental.com     14-FEB-96 Male     $32,000.00 08-MAR-20          1 11
        13 Jan             Mitchell   Mechanics                      516-894-7506 jm@employee.carrental.com      10-MAR-97 Female   $32,000.00 14-JUL-20          2 2
        15 Cliggord        Frazier    Sales Reps                     516-897-7506 cf@employee.carrental.com      06-MAY-92 Male     $32,000.00 12-DEC-20          3 14

15 rows selected.

SQL> SELECT clientNo, FName, LName, Phone, Email,
  2         NVL(Webaddress, ' --- ') WebAddress,
  3         Street, City, State, ZipCode
  4    FROM Client;

CLIENTNO FNAME      LNAME        PHONE        EMAIL                       WEBADDRESS                STREET                  CITY                 ST ZIPCO
-------- ---------- ------------ ------------ --------------------------- ------------------------- ----------------------- -------------------- -- -----
    1011 Florencio  Barton       555-566-6313 fbarton@alliedcorp.org      www.alliedcorp.org        8425 North Ave.         Philadelphia         PA 30213
    1012 Lenny      Abramowski   555-637-7282 labramowski@mainquartz.org  www.mainquartz.org        56 South Newcastle Dr.  Tacoma               WA 02125
    1013 Zach       Demerritt    555-426-8792 zachd@greensworthgroup.org  www.greensworthgroup.org  8895 Bay Meadows Ave.   Lemont               IL 60439
    1014 Erin       Gonzalez     555-436-7639 egonzalez@projectgr.gov     www.projectgr.gov         851 Gregory Ave.        Park Ridge           IL 60068
    1015 Evelyn     Chen         555-637-4583 cevelyn@lansinghigh.edu     www.lansinghigh.edu       8 Foster Ave.           Monroeville          PA 48910
    1016 Robbie     Basse        555-783-0828 rbasse@canyonview.edu       www.canyonview.edu        82 Mechanic Avenue      Lake Villa           IL 60046
    1017 Addison    Mazzetta     555-468-9837 addisonm@milleniumllc.com   www.milleniumllc.com      237 Prospect Dr.        Dallas               TX 29577
    1018 Lucy       Descheneaux  555-898-5435 lucyd@pellelectric.com      www.pellelectric.com      9652 Cambridge Lane     Avon Lake            OH 44012
    1019 Rosa       Santiago     555-083-7972 s.rosa@capitalgrid.net      www.capitalgrid.net       731 St Louis Ave.       Feasterville Trevose PA 19053
    1020 Beatrice   Ginsburg     555-537-9837 bginsburg@nextgen.io        www.nextgen.io            811 Sierra St.          Vernon Hills         IL 60061
    1021 Wilbert    Richner      555-974-7942 wrichner1@outlook.com        ---                      38 Amherst St.          Pottstown            PA 19464
    1022 Jacob      Wu           555-573-5375 jacob.wu@gmail.com           ---                      398 St Paul St.         Plano                TX 29063

12 rows selected.

SQL> SELECT outNo, Street, City, State, ZipCode, Phone, ManagerNo
  2    FROM Outlet;

     OUTNO STREET               CITY                 ST ZIPCO PHONE         MANAGERNO
---------- -------------------- -------------------- -- ----- ------------ ----------
         1 Cottages             Corpus Christi       TX 73301 805-250-4174          5
         2 Stable Wynd          Anaheim              PA 15417 518-952-9887          3
         3 Mountfield Rd        Fort Wayne           IL 46201 443-760-6920          9
         4 Brooks Boulevard     Seatlle              WA 20001 859-359-5243          2

SQL>
SQL> SELECT LicenseNo, Make, Model, Color, Year, NoDoors,
  2         TO_CHAR(Capacity, '999,990') Capacity,
  3         TO_CHAR(DailyRate, '$999,990.99') DailyRate,
  4         InspectionDate, outNo
  5    FROM Vehicle;

LICENSE MAKE            MODEL                COLOR                      YEAR    NODOORS CAPACITY DAILYRATE    INSPECTIO      OUTNO
------- --------------- -------------------- -------------------- ---------- ---------- -------- ------------ --------- ----------
ABC1234 Mercedes-Benz   SL65 AMG             Black                      2009          4      800      $392.50 03-SEP-20          1
MNO7281 Audi            A3                   Black                      2013          4    1,500      $487.50 23-APR-22          4
NYO8364 Audi            R8                   White                      2015          2    1,500      $512.50 19-MAY-21          1
PMB7810 Hyundai         Accent               Black                      2016          4      900      $546.50 06-MAR-22          2
PMO4452 Hyundai         Elantra              Gray                       2016          4    1,500      $569.50 17-SEP-20          2
RES9829 Hyundai         Azera                White                      2017          4    1,400      $578.50 18-OCT-21          3
BAT9822 Audi            R8                   Gray                       2015          4    1,500      $512.50 19-JAN-22          2
BJK6371 Mercedes-Benz   AMG CLS 53           Black                      2020          2      850      $882.50 01-DEC-21          3
XYZ4526 Toyota          Avalon               Silver                     2021          4    1,200      $906.50 15-NOV-20          4

9 rows selected.

SQL> SELECT RentalNo, StartDate, NVL(TO_CHAR(ReturnDate, 'DD-MON-YYYY'), ' --- ') ReturnDate,
  2         TO_CHAR(MileageBefore, '999,990') MileageBefore,
  3         NVL(TO_CHAR(MileageAfter, '999,990'), ' --- ') MileageAfter,
  4         InsuranceType, ClientNo, LicenseNo
  5    FROM RAgreement;

  RENTALNO STARTDATE RETURNDATE           MILEAGEB MILEAGEA INSURANCETYPE                    CLIENTNO LICENSE
---------- --------- -------------------- -------- -------- ------------------------------ ---------- -------
      2022 13-JUN-22 15-JUL-2022            12,341   13,456 Liability Coverage                   1012 XYZ4526
      2023 14-JUN-22 21-JUN-2022            16,786   17,864 Collision Coverage                   1011 BJK6371
      2024 23-JUN-22 30-SEP-2022           156,787  196,789 Collision Coverage                   1015 ABC1234
      2025 01-JUL-22 15-JUL-2022           135,268  136,023 Comprehensive Coverage               1016 MNO7281
      2026 08-JUL-22 16-JUL-2022            17,865   18,356 Liability Coverage                   1022 BJK6371
      2027 17-JUL-22 01-AUG-2022            90,352   91,567 Comprehensive Coverage               1017 PMO4452
      2028 30-JUL-22 13-AUG-2022            18,356   19,125 Collision Coverage                   1015 BAT9822
      2029 05-AUG-22 30-SEP-2022            13,456   15,113 Liability Coverage                   1012 XYZ4526
      2030 12-AUG-22 31-AUG-2022            91,567   92,078 Personal Injury Protection           1011 PMO4452
      2031 19-AUG-22 01-SEP-2022            92,789   93,452 Collision Coverage                   1014 NYO8364
      2032 03-SEP-22 03-OCT-2022            19,125   21,065 Personal Injury Protection           1020 BJK6371
      2033 18-SEP-22 24-SEP-2022            93,452   93,999 Collision Coverage                   1011 NYO8364
      2034 20-SEP-22 12-OCT-2022            82,683   83,902 Liability Coverage                   1014 RES9829
      2035 25-SEP-22 23-NOV-2022            92,078   94,003 Comprehensive Coverage               1019 PMO4452
      2036 29-SEP-22 05-NOV-2022            93,999   95,123 Collision Coverage                   1018 NYO8364
      2037 15-OCT-22 29-OCT-2022           196,789  197,128 Liability Coverage                   1022 ABC1234
      2038 17-OCT-22 03-NOV-2022            83,903   85,235 Collision Coverage                   1021 RES9829
      2039 17-OCT-22 21-NOV-2022            21,065   23,104 Liability Coverage                   1013 BJK6371
      2040 01-NOV-22 15-NOV-2022           197,128  197,467 Comprehensive Coverage               1022 ABC1234
      2041 08-NOV-22 25-NOV-2022            85,235   87,689 Collision Coverage                   1013 RES9829
      2042 11-NOV-22 22-NOV-2022            95,123   96,034 Liability Coverage                   1017 NYO8364
      2043 13-NOV-22 25-NOV-2022            90,563   93,173 Comprehensive Coverage               1019 PMB7810
      2044 22-NOV-22  ---                   23,104  ---     Collision Coverage                   1015 BJK6371

23 rows selected.

SQL> SELECT ReportNum, DateChecked, Comments, EmpNo, LicenseNo, RentalNo
  2    FROM FaultReport;

 REPORTNUM DATECHECK COMMENTS                                                EMPNO LICENSE   RENTALNO
---------- --------- -------------------------------------------------- ---------- ------- ----------
         1 16-JUL-22 Flat Tire                                                   8 BJK6371       2026
         2 22-JUN-22 Scratch on the left side                                   13 BJK6371       2023
         3 15-JUL-22 Front Windshield Wiper Broken                               4 XYZ4526       2022
         4 14-AUG-22 Cracked side view mirror                                    2 BJK6371       2028
         5 31-AUG-22 Malfunctioning brake                                        5 PMO4452       2030
         6 03-OCT-22 Broken headlights                                           8 BJK6371       2032
         7 12-OCT-22 Scratched side view mirror                                 13 RES9829       2034
         8 01-AUG-22 Scratch on the right side                                   4 PMO4452       2027
         9 23-NOV-22 Carplay not working                                         8 PMO4452       2035
        10 30-SEP-22 High Beam broken                                            2 XYZ4526       2029
        11 29-OCT-22 Cracked side window                                         5 ABC1234       2037
        12 25-NOV-22 Cracked Front Windshield                                    8 RES9829       2041

12 rows selected.


PART II - B
Query #1
SQL> SELECT rentalno, licenseno, outno, make, model, year,
  2         TO_CHAR(startdate, 'DD-MON-YYYY HH24:MI') AS start_datetime,
  3         NVL(TO_CHAR(returndate, 'DD-MON-YYYY HH24:MI'), '--') AS return_datetime,
  4         NVL(TO_CHAR(last_checked, 'DD-MON-YYYY') , '--') AS date_last_checked
  5    FROM ragreement JOIN vehicle USING (licenseno)
  6                    LEFT OUTER JOIN (SELECT licenseno, MAX(datechecked) AS last_checked
  7                                       FROM faultreport
  8                                      WHERE EXTRACT (MONTH FROM SYSDATE) - EXTRACT (MONTH FROM datechecked) = 1
  9                                      GROUP BY licenseno) USING (licenseno)
 10   WHERE EXTRACT (MONTH FROM SYSDATE) - EXTRACT (MONTH FROM startdate) = 1;

  RENTALNO LICENSE      OUTNO MAKE            MODEL                      YEAR START_DATETIME             RETURN_DATETIME            DATE_LAST_CHECKED
---------- ------- ---------- --------------- -------------------- ---------- -------------------------- -------------------------- --------------------
      2041 RES9829          3 Hyundai         Azera                      2017 08-NOV-2022 13:16          25-NOV-2022 12:39          25-NOV-2022
      2044 BJK6371          3 Mercedes-Benz   AMG CLS 53                 2020 22-NOV-2022 15:47          --                         --
      2043 PMB7810          2 Hyundai         Accent                     2016 13-NOV-2022 11:27          25-NOV-2022 16:19          --
      2040 ABC1234          1 Mercedes-Benz   SL65 AMG                   2009 01-NOV-2022 15:11          15-NOV-2022 11:01          --
      2042 NYO8364          1 Audi            R8                         2015 11-NOV-2022 14:29          22-NOV-2022 10:13          --

Query #2
SQL> SELECT NVL(TO_CHAR(outNo), 'Monthly Total:') AS "Outlet",
  2  	    SUM(DECODE(EXTRACT(Month FROM StartDate), 7, 1, 0)) AS "July",
  3  	    SUM(DECODE(EXTRACT(Month FROM StartDate), 8, 1, 0)) AS "August",
  4  	    SUM(DECODE(EXTRACT(Month FROM StartDate), 9, 1, 0)) AS "September",
  5  	    COUNT(RentalNo) AS "Outlet Total:"
  6    FROM OUTLET JOIN VEHICLE    USING (OutNo)
  7                JOIN RAGREEMENT USING (LicenseNo)
  8   WHERE EXTRACT(Month FROM StartDate) IN (7,8,9)
  9     AND EXTRACT(Year FROM StartDate) = 2022
 10   GROUP BY ROLLUP(outNo)
 11   ORDER BY 1;

Outlet                                         July     August  September Outlet Total:                                                                                                                 
---------------------------------------- ---------- ---------- ---------- -------------                                                                                                                 
1                                                 0          1          2             3                                                                                                                 
2                                                 2          1          1             4                                                                                                                 
3                                                 1          0          2             3                                                                                                                 
4                                                 1          1          0             2                                                                                                                 
Monthly Total:                                    4          3          5            12   

Query #3
SQL> SELECT licenseno, make, model,
  2         (EXTRACT (YEAR FROM SYSDATE) - year) AS "Age (Yrs)",
  3         TO_CHAR(SUM(mileageafter - mileagebefore), '9,999,990') AS miles_driven,
  4         TO_CHAR(MAX(mileageafter), '9,999,990') AS odometer_reading,
  5         COUNT(DISTINCT reportnum) AS "# of Fault Reports"
  6    FROM vehicle LEFT OUTER JOIN ragreement  USING (licenseno)
  7                 LEFT OUTER JOIN faultreport USING (licenseno)
  8   WHERE (EXTRACT (YEAR FROM SYSDATE) - year) >= 5
  9   GROUP BY licenseno, make, model, (EXTRACT (YEAR FROM SYSDATE) - year);

LICENSE MAKE            MODEL                 Age (Yrs) MILES_DRIV ODOMETER_R # of Fault Reports
------- --------------- -------------------- ---------- ---------- ---------- ------------------
ABC1234 Mercedes-Benz   SL65 AMG                     13     40,680    197,467                  1
MNO7281 Audi            A3                            9        755    136,023                  0
PMO4452 Hyundai         Elantra                       6     10,953     94,003                  3
BAT9822 Audi            R8                            7        769     19,125                  1
NYO8364 Audi            R8                            7      3,245     96,034                  0
RES9829 Hyundai         Azera                         5     10,010     87,689                  2
PMB7810 Hyundai         Accent                        6      2,610     93,173                  0

7 rows selected.

Query #4
SQL> SELECT t2.Quarter, NVL(t2.Make, '---') Vehicle_Make,
  2         NVL(TO_CHAR(Likelihood, '9990.99'), '---') Likelihood, NVL(Rentals, 0) Rentals,
  3         (CASE WHEN Likelihood IS NULL THEN 0 ELSE rank END) rank
  4    FROM
  5  (
  6   SELECT Quarter,
  7          COUNT(ReportNum)/NULLIF(COUNT(RA.RentalNo),0) Likelihood,
  8          COUNT(RA.RentalNo) Rentals, Make,
  9          RANK() OVER (PARTITION BY Quarter ORDER BY COUNT(ReportNum)/NULLIF(COUNT(RA.RentalNo),0) DESC) rank
 10    FROM (SELECT '2022-' || level AS quarter
 11            FROM dual CONNECT BY level <= 4) quarters
 12          LEFT JOIN RAGREEMENT     RA ON    (quarter = TO_CHAR(StartDate, 'YYYY-Q'))
 13          FULL OUTER JOIN VEHICLE  V  USING (LicenseNo)
 14          LEFT JOIN FAULTREPORT    FA ON    (RA.RentalNo = FA.RentalNo)
 15   GROUP BY Quarter, Make
 16  ) t1
 17  RIGHT JOIN
 18  (
 19   SELECT DISTINCT Quarter, Make
 20     FROM (SELECT '2022-' || level AS quarter
 21             FROM dual CONNECT BY level <= 4)
 22           CROSS JOIN VEHICLE
 23  ) t2
 24   ON (t1.quarter = t2.quarter) AND (t1.Make = t2.Make)
 25   ORDER BY 1;



QUARTER                                       VEHICLE_MAKE    LIKELIHO    RENTALS       RANK
--------------------------------------------- --------------- -------- ---------- ----------
2022-1                                        Mercedes-Benz   ---               0          0
2022-1                                        Audi            ---               0          0
2022-1                                        Toyota          ---               0          0
2022-1                                        Hyundai         ---               0          0
2022-2                                        Toyota              1.00          1          1
2022-2                                        Mercedes-Benz       0.50          2          2
2022-2                                        Hyundai         ---               0          0
2022-2                                        Audi            ---               0          0
2022-3                                        Toyota              1.00          1          1
2022-3                                        Hyundai             1.00          4          1
2022-3                                        Mercedes-Benz       1.00          2          1
2022-3                                        Audi                0.20          5          4
2022-4                                        Audi                0.00          1          3
2022-4                                        Toyota          ---               0          0
2022-4                                        Mercedes-Benz       0.25          4          2
2022-4                                        Hyundai             0.33          3          1

16 rows selected.

Query #5
SQL> SELECT model, "# of Vehicles",
  2         TO_CHAR(Revenue / SUM(Revenue) OVER () * 100, '9,999,990.9') AS "% of Q3 Revenue",
  3         RANK() OVER (ORDER BY Revenue DESC) AS Rank
  4    FROM (SELECT make || ' ' || model AS model,
  5                 COUNT(DISTINCT licenseno) AS "# of Vehicles",
  6                 SUM(NVL(total_days, 0)) * dailyrate AS Revenue
  7            FROM (SELECT licenseno, make, model, dailyrate
  8                    FROM vehicle) LEFT OUTER JOIN
  9                 (SELECT licenseno, SUM(TRUNC(returndate - startdate)) AS Total_Days
 10                    FROM ragreement
 11                   WHERE TO_CHAR(returndate, 'YYYY-Q') = '2022-3'
 12                   GROUP BY licenseno) USING (licenseno)
 13   GROUP BY make, model, dailyrate)
 14   ORDER BY Rank;

MODEL                                # of Vehicles % of Q3 Reve       RANK
------------------------------------ ------------- ------------ ----------
Toyota Avalon                                    1         47.4          1
Mercedes-Benz SL65 AMG                           1         23.3          2
Hyundai Elantra                                  1         11.3          3
Audi R8                                          2         10.2          4
Audi A3                                          1          4.1          5
Mercedes-Benz AMG CLS 53                         1          3.7          6
Hyundai Azera                                    1          0.0          7
Hyundai Accent                                   1          0.0          7

8 rows selected.

Query #6
SQL> SELECT Level,
  2         LPAD(' ', 3*(LEVEL-1)) || EmpNo || '  ' || FName || ' ' || LName "Employee",
  3         Position, Street, City, State, Zipcode, NVL(num_of_report,0) num_of_report
  4    FROM EMPLOYEE LEFT JOIN OUTLET USING (OutNo)
  5  LEFT JOIN
  6  (
  7  SELECT EmpNo, COUNT(ReportNum) num_of_report
  8    FROM EMPLOYEE LEFT JOIN FAULTREPORT USING (EmpNo)
  9   WHERE SYSDATE - DateChecked <= 90
 10   GROUP BY EmpNo
 11  ) USING (EmpNo)
 12  START WITH EmpNo IN
 13  (SELECT EmpNo
 14     FROM EMPLOYEE
 15    WHERE Super_No IS NULL
 16  )
 17  CONNECT BY PRIOR EmpNo = Super_No;

     LEVEL Employee                       POSITION                       STREET               CITY                 ST ZIPCO NUM_OF_REPORT
---------- ------------------------------ ------------------------------ -------------------- -------------------- -- ----- -------------
         1 3  Reid Grimes                 Administrative Assistants      Stable Wynd          Anaheim              PA 15417             0
         2    11  Tracey Elliott          Administrative Assistants      Stable Wynd          Anaheim              PA 15417             0
         3       6  Karen Greene          Administrative Assistants      Mountfield Rd        Fort Wayne           IL 46201             0
         3       12  Troy Edwards         Administrative Assistants      Cottages             Corpus Christi       TX 73301             0
         1 5  Sumaiya Cannon              Mechanics                      Cottages             Corpus Christi       TX 73301             1
         2    2  Raja Byers               Mechanics                      Brooks Boulevard     Seatlle              WA 20001             1
         3       4  Farrah Adams          Mechanics                      Stable Wynd          Anaheim              PA 15417             0
         3       8  Tommy Burke           Mechanics                      Mountfield Rd        Fort Wayne           IL 46201             3
         3       13  Jan Mitchell         Mechanics                      Stable Wynd          Anaheim              PA 15417             1
         1 9  Tabitha Vaughn              Sales Reps                     Mountfield Rd        Fort Wayne           IL 46201             0
         2    14  Ted Payne               Sales Reps                     Mountfield Rd        Fort Wayne           IL 46201             0
         3       1  Leja Mckee            Sales Reps                     Cottages             Corpus Christi       TX 73301             0
         3       7  Emma Lawrence         Sales Reps                     Brooks Boulevard     Seatlle              WA 20001             0
         3       10  Tara Rodriquez       Sales Reps                     Stable Wynd          Anaheim              PA 15417             0
         3       15  Cliggord Frazier     Sales Reps                     Mountfield Rd        Fort Wayne           IL 46201             0

15 rows selected.

Query #7
SQL> SELECT outno,
  2         NVL(No_Of_Vehicles, 0) AS No_Of_Vehicles,
  3         NVL(No_Of_Rentals, 0) AS No_Of_Rentals,
  4         TO_CHAR(NVL(Avg_Distance_Driven, 0), '9,999,990.9') AS Avg_Distance_Driven,
  5         NVL(No_Of_Emps, 0) AS No_Of_Emps,
  6         TO_CHAR(NVL(No_Of_Rentals, 0) / No_Of_Emps, '9,999,990.9') AS Rentals_Per_Emp
  7    FROM (SELECT outno, COUNT(empno) AS No_Of_Emps
  8            FROM outlet JOIN employee USING (outno)
  9           GROUP BY outno) LEFT OUTER JOIN
 10         (SELECT outno, COUNT(rentalno) AS No_Of_Rentals, AVG(mileageafter - mileagebefore) AS Avg_Distance_Driven
 11            FROM outlet JOIN vehicle    USING (outno)
 12                        JOIN ragreement USING (licenseno)
 13           WHERE TRUNC(SYSDATE - startdate) <= 60
 14           GROUP BY outno) USING (outno) LEFT OUTER JOIN
 15         (SELECT outno, COUNT(licenseno) No_Of_Vehicles
 16            FROM outlet JOIN vehicle USING (outno)
 17           GROUP BY outno) USING (outno);

     OUTNO NO_OF_VEHICLES NO_OF_RENTALS AVG_DISTANCE NO_OF_EMPS RENTALS_PER_
---------- -------------- ------------- ------------ ---------- ------------
         1              2             3        529.7          3          1.0
         4              2             0          0.0          2          0.0
         2              3             1      2,610.0          5          0.2
         3              2             4      1,941.7          5          0.8

Query #8
SQL> SELECT * FROM
  2  (
  3   SELECT clientno || ' - ' || FName || ' ' || LName "Client", TO_CHAR(SUM((ReturnDate - StartDate)*DailyRate), '$99,990.99') "Revenue",
  4          RANK() OVER (ORDER BY SUM((ReturnDate - StartDate)*DailyRate) DESC) rank
  5     FROM CLIENT JOIN RAGREEMENT USING (ClientNo)
  6                 JOIN VEHICLE    USING (LicenseNo)
  7    WHERE ReturnDate IS NOT NULL
  8      AND EXTRACT(Year FROM NVL(ReturnDate,SYSDATE)) = 2022
  9    GROUP BY clientno || ' - ' || FName || ' ' || LName
 10  )
 11  WHERE rank = 1;

Client                                                                               Revenue           RANK
------------------------------------------------------------------------------------ ----------- ----------
1012 - Lenny Abramowski                                                               $79,790.89          1  

Query #9
SQL> SELECT outno, In_Table.state, cust_in_state, rent_in_state,
  2         TO_CHAR(cust_in_state / all_cust * 100, '9,999,990.9') AS "% of Cust_In_State",
  3         TO_CHAR(rent_in_state / all_rent * 100, '9,999,990.9') AS "% of Rentals_In_State"
  4    FROM (SELECT outno, outlet.state AS state,
  5                 COUNT(DISTINCT clientno) AS Cust_In_State,
  6                 COUNT(rentalno) AS Rent_In_State
  7            FROM outlet LEFT OUTER JOIN vehicle    USING (outno)
  8                        LEFT OUTER JOIN ragreement USING (licenseno)
  9                        LEFT OUTER JOIN client     USING (clientno)
 10           WHERE outlet.state = client.state
 11           GROUP BY outno, outlet.state) In_Table JOIN
 12        (SELECT outno, outlet.state AS state,
 13                COUNT(DISTINCT clientno) AS All_Cust,
 14                COUNT(rentalno) AS All_Rent
 15           FROM outlet LEFT OUTER JOIN vehicle    USING (outno)
 16                       LEFT OUTER JOIN ragreement USING (licenseno)
 17                       LEFT OUTER JOIN client     USING (clientno)
 18          GROUP BY outno, outlet.state) All_Table USING (outno);

     OUTNO ST CUST_IN_STATE RENT_IN_STATE % of Cust_In % of Rentals
---------- -- ------------- ------------- ------------ ------------
         4 WA             1             2         50.0         66.7
         3 IL             3             4         42.9         50.0
         1 TX             2             3         33.3         42.9
         2 PA             3             4         75.0         80.0

Query #10
SQL> SELECT ClientType,
  2  	    COUNT(DISTINCT clientno) AS No_Of_Clients,
  3  	    COUNT(rentalno) AS Last_Q_Rentals
  4    FROM (SELECT clientno,
  5  	            (CASE WHEN LOWER(REGEXP_SUBSTR(WEBADDRESS,'[.][^.]*$')) IS NULL
  6  		           THEN 'Not Available'
  7  		          WHEN LOWER(REGEXP_SUBSTR(WEBADDRESS,'[.][^.]*$')) = '.com'
  8  		           THEN 'For-Profit Company'
  9  		          WHEN LOWER(REGEXP_SUBSTR(WEBADDRESS,'[.][^.]*$')) = '.gov'
 10  		           THEN 'Government Agency'
 11  		          WHEN LOWER(REGEXP_SUBSTR(WEBADDRESS,'[.][^.]*$')) = '.edu'
 12  		           THEN 'Educational Institution'
 13  		          WHEN LOWER(REGEXP_SUBSTR(WEBADDRESS,'[.][^.]*$')) = '.org'
 14  		           THEN 'Not-for-Profit Organization'
 15  	                  ELSE 'Other'
 16  	              END) AS ClientType
 17  	       FROM client) LEFT OUTER JOIN
 18  	     (SELECT clientno, rentalno
 19  	        FROM ragreement
 20  	       WHERE TO_NUMBER(TO_CHAR(SYSDATE, 'Q')) - TO_NUMBER(TO_CHAR(StartDate, 'Q')) = 1) USING (clientno)
 21   GROUP BY clienttype;


CLIENTTYPE                  NO_OF_CLIENTS LAST_Q_RENTALS                                                                                                                                                
--------------------------- ------------- --------------                                                                                                                                                
Not-for-Profit Organization             3              3                                                                                                                                                
Government Agency                       1              2                                                                                                                                                
Educational Institution                 2              2                                                                                                                                                
For-Profit Company                      2              2                                                                                                                                                
Other                                   2              2                                                                                                                                                
Not Available                           2              1                                                                                                                                                

6 rows selected.
REFERENCES
AbsractAPI. (2022, January 5). Ultimate Guide to validating emails with regex (2022). Ultimate Guide to Validating Emails with Regex (2022). Retrieved November 20, 2022, from https://www.abstractapi.com/tools/email-regex-guide
UI Bakery. (n.d.). URL REGEX. URL regex | UI Bakery. Retrieved November 20, 2022, from https://uibakery.io/regex-library/url
Baker, C. (2022, November 16). 6 types of car insurance: What you need in 2022. WalletHub. Retrieved November 25, 2022, from https://wallethub.com/edu/ci/types-of-car-insurance/9636
