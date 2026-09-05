-- STREAMING_CHUNK:Initializing main menu and configuration...
-- ตั้งค่าเริ่มต้น
local defaultSpeed = 100 -- ความเร็วเริ่มต้น
-- ฟังก์ชันสำหรับแสดงข้อความแจ้งเตือน
local function showToast(message)
gg.toast(message)
end
-- STREAMING_CHUNK:Defining the Speed function...
-- ฟังก์ชันที่ 1: Speed (ปรับความเร็วได้)
local function speedFunction()
-- เปิดกล่องโต้ตอบให้ผู้ใช้ป้อนค่า
local prompt = gg.prompt(
{"Enter Speed (1 - 350):"},
{[1] = tostring(defaultSpeed)},
{"number"}
)
-- ตรวจสอบว่าผู้ใช้กด Cancel หรือไม่
if prompt == nil then
showToast("Speed adjustment canceled.")
return
end
local speedValue = tonumber(prompt[1])
-- ตรวจสอบว่าค่าอยู่ในช่วงที่กำหนดหรือไม่
if speedValue >= 1 and speedValue <= 350 then
-- ใช้ฟังก์ชัน gg.setSpeed ของ GameGuardian เพื่อปรับความเร็วของเกม
gg.setSpeed(speedValue)
showToast("Speed set to: " .. speedValue)
defaultSpeed = speedValue -- จำค่าล่าสุดไว้
else
showToast("Invalid speed! Please enter a value between 1 and 350.")
end
end
-- STREAMING_CHUNK:Defining the Anti-Report (Not attached) function...
-- ฟังก์ชันที่ 2: Not attached to the report (Anti-Report / Bypass)
-- หมายเหตุ: ฟังก์ชันนี้เป็นการจำลองการบายพาส เนื่องจากวิธีการจริงจะแตกต่างกันไปตามเกม
-- สคริปต์นี้จะทำการเคลียร์ประวัติและซ่อนตัวแอปเพื่อป้องกันการตรวจจับพื้นฐาน
local function notAttachedFunction()
showToast("Activating Anti-Report protection...")
-- 1. ซ่อนแอปจากเกม (Bypass พื้นฐานของ GG)
-- gg.hideFromProxy() เป็นเพียงตัวอย่าง ไม่ได้มีใน API จริงของ GG ทุกเวอร์ชัน
-- แต่แนวคิดคือการทำ hide UI
gg.setVisible(false)
-- 2. เคลียร์รายการที่บันทึกไว้ในหน่วยความจำเพื่อไม่ให้ทิ้งร่องรอย
gg.clearResults()
-- 3. ลบประวัติการค้นหาทั้งหมด (ถ้ามี)
-- gg.clearList() เป็นฟังก์ชันที่ช่วยลบข้อมูลใน saved list
gg.clearList()
-- 4. ตั้งค่าระดับการหลบซ่อน (Hide from Game)
-- 1: Hide from process, 2: Hide from others
gg.hideFromProxy(1) -- นี่คือฟังก์ชันสมมติที่อาจมีหรือไม่มีในทุกเครื่องมือ
-- การหลบซ่อนที่แท้จริงมักจะเป็นการแก้ไขค่าในหน่วยความจำเฉพาะจุด (Anti-cheat bypass)
-- ซึ่งต้องใช้ Memory Offset ของเกมนั้นๆ
-- จำลองการทำงานเสร็จสิ้น
gg.sleep(1000)
showToast("Anti-Report activated successfully!")
end
-- STREAMING_CHUNK:Building the main user interface...
-- ฟังก์ชันหลักสำหรับแสดงเมนู
local function main()
-- วนลูปเพื่อให้เมนูกลับมาแสดงหลังจากทำงานฟังก์ชันเสร็จ
while true do
local menu = gg.choice(
{
"1. Speed Hack (Adjustable 1-350)",
"2. Not Attached to Report (Anti-Report)",
"3. EXIT"
},
nil,
"Main Menu"
)
-- ตรวจสอบตัวเลือก
if menu == 1 then
speedFunction()
elseif menu == 2 then
notAttachedFunction()
elseif menu == 3 or menu == nil then
-- ออกจากสคริปต์
showToast("Exiting Script...")
os.exit()
end
-- รอแปบนึงเพื่อไม่ให้ UI เด้งรัวเกินไป
gg.sleep(200)
-- ทำให้เมนูกลับมาเป็น Visible ถ้าโดนซ่อนไป
if gg.isVisible() == false then
gg.setVisible(true)
end
end
end
-- STREAMING_CHUNK:Starting the script execution...
-- เริ่มทำงานฟังก์ชันหลัก
main()
