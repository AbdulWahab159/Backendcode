# Backendcode
import 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'package:firebase_auth/firebase_auth.dart';
import 'package:cloud_firestore/cloud_firestore.dart';
import 'firebase_options.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();

  await Firebase.initializeApp(
    options: DefaultFirebaseOptions.currentPlatform,
  );

  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return const MaterialApp(
      debugShowCheckedModeBanner: false,
      home: HospitalBackend(),
    );
  }
}

class HospitalBackend {
  final FirebaseFirestore db = FirebaseFirestore.instance;
  final FirebaseAuth auth = FirebaseAuth.instance;

  // REGISTER
  Future<User?> register(String email, String password) async {
    final result = await auth.createUserWithEmailAndPassword(
      email: email,
      password: password,
    );
    return result.user;
  }

  // LOGIN
  Future<User?> login(String email, String password) async {
    final result = await auth.signInWithEmailAndPassword(
      email: email,
      password: password,
    );
    return result.user;
  }

  // LOGOUT
  Future<void> logout() async {
    await auth.signOut();
  }

  // ADD PATIENT
  Future<void> addPatient(
    String name,
    int age,
    String gender,
    String phone,
  ) async {
    await db.collection('patients').add({
      'name': name,
      'age': age,
      'gender': gender,
      'phone': phone,
      'createdAt': FieldValue.serverTimestamp(),
    });
  }

  // GET PATIENTS
  Stream<QuerySnapshot> getPatients() {
    return db.collection('patients').snapshots();
  }

  // UPDATE PATIENT
  Future<void> updatePatient(
    String id,
    Map<String, dynamic> data,
  ) async {
    await db.collection('patients').doc(id).update(data);
  }

  // DELETE PATIENT
  Future<void> deletePatient(String id) async {
    await db.collection('patients').doc(id).delete();
  }

  // ADD DOCTOR
  Future<void> addDoctor(
    String name,
    String specialization,
    String phone,
  ) async {
    await db.collection('doctors').add({
      'name': name,
      'specialization': specialization,
      'phone': phone,
      'createdAt': FieldValue.serverTimestamp(),
    });
  }

  // GET DOCTORS
  Stream<QuerySnapshot> getDoctors() {
    return db.collection('doctors').snapshots();
  }

  // DELETE DOCTOR
  Future<void> deleteDoctor(String id) async {
    await db.collection('doctors').doc(id).delete();
  }

  // ADD APPOINTMENT
  Future<void> addAppointment(
    String patientName,
    String doctorName,
    String date,
    String time,
  ) async {
    await db.collection('appointments').add({
      'patientName': patientName,
      'doctorName': doctorName,
      'date': date,
      'time': time,
      'status': 'Pending',
      'createdAt': FieldValue.serverTimestamp(),
    });
  }

  // GET APPOINTMENTS
  Stream<QuerySnapshot> getAppointments() {
    return db.collection('appointments').snapshots();
  }

  // UPDATE APPOINTMENT
  Future<void> updateAppointment(
    String id,
    String status,
  ) async {
    await db.collection('appointments').doc(id).update({
      'status': status,
    });
  }

  // DELETE APPOINTMENT
  Future<void> deleteAppointment(String id) async {
    await db.collection('appointments').doc(id).delete();
  }

  // ADD MEDICAL RECORD
  Future<void> addMedicalRecord(
    String patientId,
    String diagnosis,
    String prescription,
  ) async {
    await db.collection('medical_records').add({
      'patientId': patientId,
      'diagnosis': diagnosis,
      'prescription': prescription,
      'createdAt': FieldValue.serverTimestamp(),
    });
  }

  // GET MEDICAL RECORDS
  Stream<QuerySnapshot> getMedicalRecords() {
    return db.collection('medical_records').snapshots();
  }

  // DELETE MEDICAL RECORD
  Future<void> deleteMedicalRecord(String id) async {
    await db.collection('medical_records').doc(id).delete();
  }
}
