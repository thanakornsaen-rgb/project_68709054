<template>
  <div class="container mt-4">
    <!-- หัวข้อหน้า -->
    <h2 class="mb-3">ข้อมูลติดต่อ</h2>
    
    <!-- ตารางแสดงข้อมูลลูกค้า -->
     <div class="text-end mb-2">
      <a href="/add_contact" class="btn btn-secondary">Add+</a>


     </div>
    <table class="table table-bordered table-striped">
      <thead class="table-dark">
        <tr>
          <th>รหัสการติดต่อ</th>     <!-- contact_id -->
          <th>หัวข้อ</th>            <!-- subject -->
          <th>รายละเอียด</th>        <!-- detail -->
          <th>ชื่อ-นามสกุล</th>       <!-- fullname -->
          <th>อีเมล</th>      <!-- email -->
          <th>วันเวลาที่เพิ่มข้อมูล</th>      <!-- created_at -->
        </tr>
      </thead>

      <tbody>
        <!-- วนลูปข้อมูล customers -->
        <tr v-for="(item) in contact" :key="item.contact_id">
          <td>{{ item.contact_id }}</td> <!-- รหัสลูกค้า -->
          <td>{{ item.subject }}</td>   <!-- หัวข้อ -->
          <td>{{ item.detail }}</td>    <!-- รายละเอียด -->
          <td>{{ item.fullname }}</td>       <!-- ชื่อ-นามสกุล -->
          <td>{{ item.email }}</td>    <!-- อีเมล -->
          <td>{{ item.created_at }}</td>     <!-- วันเวลาที่เพิ่มข้อมูล -->
        </tr>
      </tbody>
    </table>

    <!-- Loading: แสดงระหว่างรอข้อมูล -->
    <div v-if="loading" class="text-center">
      <p>กำลังโหลดข้อมูล...</p>
    </div>

    <!-- Error: แสดงเมื่อเกิดข้อผิดพลาด -->
    <div v-if="error" class="alert alert-danger">
      {{ error }}
    </div>
  </div>
</template>

<script>
// import ฟังก์ชันจาก Vue (Composition API)
import { ref, onMounted } from "vue";

export default {
  name: "ContactList", // ชื่อ component

  setup() {
    // -----------------------------
    // state (ตัวแปร reactive)
    // -----------------------------
    const contact = ref([]); // เก็บข้อมูลลูกค้า (array)
    const loading = ref(true); // สถานะโหลดข้อมูล
    const error = ref(null);   // เก็บ error

    // -----------------------------
    // ฟังก์ชันดึงข้อมูลจาก API
    // -----------------------------
    const fetchdata = async () => {
      try {
        // เรียก API (PHP)
        const response = await fetch("http://localhost/project_week3_vue/php_api/show_contact.php");

        // ตรวจสอบว่าการเรียกสำเร็จหรือไม่
        if (!response.ok) {
          throw new Error("ไม่สามารถดึงข้อมูลได้");
        }

        // แปลง response เป็น JSON
        contact.value = await response.json();

      } catch (err) {
        // ถ้า error ให้เก็บข้อความไว้แสดง
        error.value = err.message;

      } finally {
        // ไม่ว่าจะสำเร็จหรือ error ให้หยุด loading
        loading.value = false;
      }
    };

    // -----------------------------
    // lifecycle: ทำงานเมื่อ component โหลดเสร็จ
    // -----------------------------
    onMounted(() => {
      fetchdata(); // เรียก API ทันที
    });

    // -----------------------------
    // return ค่าไปใช้ใน template
    // -----------------------------
    return {
      contact,
      loading,
      error
    };
  }
};
</script>