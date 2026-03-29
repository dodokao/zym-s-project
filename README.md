# zym-s-project
# 赵怡茗-25331042-第二次人工智能编程作业
## 1. 任务拆解与 AI 协作策略
我先阅读完材料，明确了：面向对象编程、模块限制等限制并告知ai。（但是即使告知了还是部分没做到）然后将任务拆分为：
  1.下载名单并将其转入py的文件夹
  2.告知名单的位置，让其打开读入
  3.加载学生文件后，去掉表头，将对象存储
  4.实现通过学号查找的任务（注意学号格式错误的情况）
  5.实现随机点名功能
  6.输出时间
  7.生成座位表（先打乱，在输出，也需要建新文件）
  8.打印准考证，建文件夹，建不同的准考证，每一个都要规范化输入
## 2. 核心 Prompt 迭代记录
在1中我告诉ai用student类，面向对象封装学生信息。第二部分，增加了len（parts>6)的判断，保证输入的格式的正确。也加入了try except，排除filenofounderror 。第四部分给出，提示ai多一点提示语告诉用户要输入学号，同时在学号出错时给出友好提示。第五部分tryexcept排除permissionerror和Oserror(其中我对oserror不熟悉，已经向ai询问得到答案：指操作系统错误）。第8部分增加准考证生成进度，便于用户明白进度。最后检查增加了静态方法（给examsystem增加了@staticmethod，检验学号格式）。这里面有一个我不熟悉的函数enumerate也已经询问ai等于遍历+自动计数）
ai存在的问题多是不够符合题目要求但是可以完成任务，所以需要用提示词改变其思路，时它能够生成方便用户使用的代码
## 3. Debug 与异常处理记录
在commit中有三四次debug，其中存在少import库（自己发现random标红并加上了import random），少定义变量（total标红，自己发现并改正），由于复制导致的缩进错误（截图给ai，ai发现的），文件输入路径的不正确（在之前已经告诉ai正确的名单所在的路径，但是他还是生成错了，手动更正），作为编号没有从1开始（检查输出结果时发现，告诉ai，ai自己修改）
在debug时发现ai比较喜欢揪着某些正确的点不放，会导致程序过于冗杂，所以使用aidebug时需要主要依赖自己的判断力
## 4. 人工代码审查 (Code Review)
在commit中每一步都有注释

import os
from typing import List
import time
import random

class Student:#先初始化数据，选择用字符串类型，同时用类分装
    def __init__(self, student_id: str, name: str, gender: str, cls: str, college: str):
        self.student_id = student_id  # 学号
        self.name = name              # 姓名
        self.gender = gender          # 性别
        self.cls = cls                # 班级
        self.college = college        # 学院

    def __str__(self):
        return f"学号：{self.student_id} | 姓名：{self.name} | 性别：{self.gender} | 班级：{self.cls} | 学院：{self.college}"


def load_students(self):
    with open(self.filename, 'r', encoding='utf-8') as f:
        next(f)
        for line in f:
            line = line.strip()
            if not line:
                continue
            parts = line.split()
            if len(parts) >= 6:
                _, name, gender, cls, student_id, college = parts
                if ExamSystem.validate_student_id(student_id):#在这里调用静态方法校验学号
                    stu = Student(student_id, name, gender, cls, college)
                    self.students.append(stu)
                else:
                    print(f"⚠️  跳过格式错误的学号：{student_id}")
# 任务1：信息初始化与查找
class ExamSystem:
    def __init__(self, filename: str):
        self.filename = filename
        self.students: List[Student] = []
        self.load_students()  # 读取在同一个文件夹中的文件（这一步需要在给ai提示词时说明文件所在位置）

    def load_students(self):
        try:#用try except处理潜在异常（filenofounderror 类型的异常）
            with open(self.filename, 'r', encoding='utf-8') as f:
                next(f)
                for line in f:
                    line = line.strip()
                    if not line:#此处去掉第一行的内容，留下的是学生名单
                        continue
                    parts = line.split()
                    if len(parts) >= 6:#此处用于确保数据是正确的，没有少名字、学号等内容
                        _, name, gender, cls, student_id, college = parts
                        stu = Student(student_id, name, gender, cls, college)
                        self.students.append(stu)
            print(f"✅ 成功加载 {len(self.students)} 名学生信息")
        except FileNotFoundError:
            print(f"❌ 错误：未找到文件 {self.filename}，请确认文件在程序同目录下！")
            self.students = []

    def find_student_by_id(self, student_id: str) -> Student | None:
        for stu in self.students:#根据学号查找对应的学生
            if stu.student_id == student_id:
                return stu
        return None#没找到返回none

    def search_menu(self):
        if not self.students:
            print("❌ 暂无学生信息，请先确认名单文件！")
            return
        stu_id = input("请输入要查询的学号：").strip()#主动发问让用户明白干什么，符合：面向对象
        stu = self.find_student_by_id(stu_id)
        if stu:
            print("\n📄 学生信息如下：")
            print(stu)
        else:
            print(f"❌ 未找到学号为【{stu_id}】的学生:)")#学号不存在时，给出友好的提示  :）

# ======================
# 程序入口（测试任务1）
# ======================
if __name__ == "__main__":
    system = ExamSystem("人工智能编程语言学生名单.txt")
    system.search_menu()

    def generate_exam_table(self):# 随机打乱学生
        shuffled_stus = random.sample(self.students, len(self.students))

        now = time.strftime("%Y-%m-%d %H:%M:%S") # 生成当前时间
        with open("考场安排表.txt", "w", encoding="utf-8") as f:#新建txt方便用户查看安排表
            f.write(f"生成时间：{now}\n")
            f.write("------------------------\n")#此处便于美观，将时间与下面的座位分开

            for idx, stu in enumerate(shuffled_stus, start=1):#随机生成
                f.write(f"座位号：{idx}  姓名：{stu.name}  学号：{stu.student_id}\n")

        print("✅ 考场安排表已生成：考场安排表.txt")#用以表现程序的进度


def generate_admission_tickets(self):
    root_folder = "准考证文件夹"#明确文件名，方便用户
    try:
        if not os.path.exists(root_folder):
            os.mkdir(root_folder)
        total = len(self.students)  # 定义总人数
        for i, stu in enumerate(self.students, start=1):#新函数
            stu_folder = os.path.join(root_folder, stu.student_id)
            if not os.path.exists(stu_folder):
                os.mkdir(stu_folder)
        for stu in self.students:
            stu_folder = os.path.join(root_folder, stu.student_id)
            if not os.path.exists(stu_folder):
                os.mkdir(stu_folder)
            ticket_file = os.path.join(stu_folder, f"{stu.student_id}_准考证.txt")
            with open(ticket_file, "w", encoding="utf-8") as f:#输入准考证内容
                # 原有写入逻辑不变
                f.write("=" * 30 + "\n")
                f.write("          考 试 准 考 证\n")
                f.write("=" * 30 + "\n\n")
                f.write(f"学号：{stu.student_id}\n")
                f.write(f"姓名：{stu.name}\n")
                f.write(f"性别：{stu.gender}\n")
                f.write(f"班级：{stu.cls}\n")
                f.write(f"学院：{stu.college}\n\n")
                f.write(f"生成时间：{time.strftime('%Y-%m-%d %H:%M:%S')}\n")
                f.write("=" * 30 + "\n")
                print(f"📄 已生成 {i}/{total}：{stu.name}（{stu.student_id}）")#加入进度提示
        print("✅ 所有学生准考证已生成完成！")
    except PermissionError:#排除permissionerror（利用try except）
        print("❌ 错误：没有权限创建文件夹或文件，请检查目录权限！")
    except OSError as e:
        print(f"❌ 生成准考证时发生错误：{str(e)}")

if __name__ == "__main__":
    system = ExamSystem("人工智能编程语言学生名单.txt")
    system.generate_admission_tickets()

# 贴入代码及人工注释
