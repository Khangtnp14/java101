-- XÓA CŨ (nếu đang tồn tại)
IF DB_ID('ASM_JAVA101') IS NOT NULL
BEGIN
    ALTER DATABASE ASM_JAVA101 SET SINGLE_USER WITH ROLLBACK IMMEDIATE;
    DROP DATABASE ASM_JAVA101;
END
GO

CREATE DATABASE ASM_JAVA101;
GO
USE ASM_JAVA101;
GO

-- 1. Bảng CATEGORIES
CREATE TABLE CATEGORIES (
    Id          VARCHAR(20)    PRIMARY KEY,
    Name        NVARCHAR(100)  NOT NULL
);

-- 2. Bảng USERS
CREATE TABLE USERS (
    Id          VARCHAR(20)    PRIMARY KEY,       -- mã đăng nhập
    Password    VARCHAR(100)   NOT NULL,
    Fullname    NVARCHAR(100)  NOT NULL,
    Birthday    DATE           NULL,
    Gender      BIT            NOT NULL,          -- 1: Nam, 0: Nữ
    Mobile      VARCHAR(20)    NULL,
    Email       VARCHAR(100)   NOT NULL,
    Role        BIT            NOT NULL           -- 1: Admin, 0: Phóng viên
);

-- 3. Bảng NEWS
CREATE TABLE NEWS (
    Id          INT            IDENTITY(1,1) PRIMARY KEY,
    Title       NVARCHAR(200)  NOT NULL,
    Content     NVARCHAR(MAX)  NOT NULL,
    Image       VARCHAR(255)   NULL,             -- VD: 'uploads/abc.png'
    PostedDate  DATE           NOT NULL DEFAULT GETDATE(),
    Author      VARCHAR(20)    NOT NULL,
    ViewCount   INT            NOT NULL DEFAULT 0,
    CategoryId  VARCHAR(20)    NOT NULL,
    Home        BIT            NOT NULL DEFAULT 0
);

ALTER TABLE NEWS 
  ADD CONSTRAINT FK_NEWS_USERS 
  FOREIGN KEY (Author) REFERENCES USERS(Id);

ALTER TABLE NEWS 
  ADD CONSTRAINT FK_NEWS_CATEGORIES 
  FOREIGN KEY (CategoryId) REFERENCES CATEGORIES(Id);

-- 4. Bảng NEWSLETTERS
CREATE TABLE NEWSLETTERS (
    Email       VARCHAR(100)   PRIMARY KEY,
    Enabled     BIT            NOT NULL DEFAULT 1
);

-- Loại tin mẫu
INSERT INTO CATEGORIES(Id, Name) VALUES
('BD_ANH',  N'Bóng đá Anh'),
('BD_TBN',  N'Bóng đá Tây Ban Nha'),
('BD_Y',    N'Bóng đá Ý'),
('C1',      N'Champions League');

-- User mẫu: 1 admin, 1 phóng viên
INSERT INTO USERS(Id, Password, Fullname, Birthday, Gender, Mobile, Email, Role) VALUES
('admin',  '123', N'Quản trị hệ thống', '1990-01-01', 1, '0900000000', 'admin@poly.com', 1),
('pv01',   '123', N'Phóng viên Bóng đá', '1995-05-10', 1, '0911111111', 'pv01@poly.com', 0);

-- 2 bản tin mẫu (nhớ copy ảnh vào thư mục /uploads trong webapp)
INSERT INTO NEWS(Title, Content, Image, Author, CategoryId, Home, ViewCount)
VALUES
(N'MU thắng kịch tính phút bù giờ',
 N'Nội dung tin chi tiết về trận đấu...',
 'uploads/mu_win.jpg',
 'pv01', 'BD_ANH', 1, 50),
(N'Real Madrid lên đỉnh châu Âu',
 N'Nội dung tin Champions League...',
 'uploads/real_c1.jpg',
 'pv01', 'C1', 1, 100);
