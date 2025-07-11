# issue12-
设计delete代码

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <assert.h>

// 模拟数据库表结构
typedef struct {
int id;
char name[50];
int ref_id; // 关联ID
} Record;

// 模拟数据库
Record* database = NULL;
int db_size = 0;

// 初始化测试数据
void init_test_data() {
db_size = 5;
database = (Record*)malloc(db_size * sizeof(Record));

// 主记录
database[0] = (Record){1, "主订单", -1};
database[1] = (Record){2, "用户信息", -1};

// 关联记录
database[2] = (Record){3, "订单详情1", 1};
database[3] = (Record){4, "订单详情2", 1};
database[4] = (Record){5, "用户地址", 2};
}

// 删除函数
void delete_record(int id) {
// 先删除关联记录
for (int i = 0; i < db_size; i++) {
if (database[i].ref_id == id) {
delete_record(database[i].id);
}
}

// 再删除主记录
for (int i = 0; i < db_size; i++) {
    if (database[i].id == id) {
        // 移动数组元素
        memmove(&database[i], &database[i+1], (db_size - i - 1) * sizeof(Record));
        db_size--;
        database = (Record*)realloc(database, db_size * sizeof(Record));
        break;
    }
}
}

// 测试用例
void test_delete_with_relations() {
init_test_data();

printf("删除前记录数: %d\n", db_size);

// 删除主订单(应该同时删除两个订单详情)
delete_record(1);

printf("删除后记录数: %d\n", db_size);
assert(db_size == 2);  // 剩下用户信息和用户地址

// 验证关联记录也被删除
int found = 0;
for (int i = 0; i < db_size; i++) {
    if (database[i].ref_id == 1) found++;
}
assert(found == 0);

free(database);
printf("测试通过!\n");
}

int main() {
test_delete_with_relations();
return 0;
}


