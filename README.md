-  検査はcomposite foreign key examClassと_fkを組み合わせて複数の検査テーブルに繋ぐ？
- 検査のクラス継承概念？
```mermaid
erDiagram

Pt_BasicInfo ||--o{ Pt_Contact : ptID

Pt_BasicInfo ||--o{ Pt_Address : ptID

Pt_BasicInfo ||--o| Pt_Summary : ptID

Pt_BasicInfo ||--o{ Pt_MedicalRecord : ptID

Exm_VisualAcuity_Summary ||--o{ Exm_VisualAcuity : k_Exm_VisualAcuity_Summary

Pt_BasicInfo ||--o{ Exm_VisualAcuity_Summary : ptID
Pt_BasicInfo ||--o{ Exm_IOP : ptID
Pt_BasicInfo ||--o{ Pt_ObjRecord : ptID
Pt_BasicInfo ||--o{ Pt_ReferralFormSet : ptID
Pt_BasicInfo ||--o{ Pt_ReferralForm_Thread : ptID
Pt_ReferralForm_Thread ||--o{ Pt_ReferralForm_Archive : k_Pt_ReferralForm_Archive
Pt_ReferralForm_Archive ||--o{ Pt_ReferralForm_Receive : k_Pt_ReferralForm_Archive
Pt_ReferralForm_Archive ||--o{ Pt_ReferralForm_Send : k_Pt_ReferralForm_Archive
Pt_ReferralForm_Receive ||--o{ Mst_RefferalDoctor : k_RefferalDoctor
Pt_ReferralForm_Receive ||--o{ Mst_ReferralHospital : k_Mst_ReferralHospital
Pt_ReferralForm_Send ||--o{ Mst_RefferalDoctor : k_RefferalDoctor
Pt_ReferralForm_Send ||--o{ Mst_ReferralHospital : k_Mst_ReferralHospital
HR_Account ||--o{ HR_Department : k_Department
HR_Account ||--o{ HR_Role : k_HR_Role
HR_Role
HR_Role o|--o{ HR_Role_Function : k_HR_Role
HR_Role_Function }o--|o HR_Function : k_HR_Function



Pt_BasicInfo {
	Num ptID PK
	Txt pt_Name
	Txt pt_familyName
	Txt pt_familyName_kana
	Txt pt_firstName
	Txt pt_firstName_kana
	Date dayOfBirth
	Txt sex
	Txt comment
	Txt isGone
	
	}
Pt_Contact {
	Txt k PK
	Num fk_ptID FK
	Txt title
	Txt type
	Txt content
	TS  created_at
	TS  modified_at
	TS  discarded_at
	Txt created_by
	Txt modified_by
	Txt discarded_by
	Txt isActive
}
Pt_Address {
	Txt k PK
	Num fk_ptID FK
	Txt title "ex自宅"
	Txt zipCode
	Txt address1
	Txt address2
	TS  created_at
	TS  modified_at
	TS  discarded_at
	Txt created_by
	Txt modified_by
	Txt discarded_by
	Txt isActive
	
}
Pt_Summary {
	Txt k PK
	Num fk_ptID FK
	Txt caution "禁忌など"
	Txt summary
	Data firstVisit[10] "初診日[10]"
	Data lastVisit "最終受診日"
	TS  created_at
	TS  modified_at
	TS  discarded_at
	Txt created_by
	Txt modified_by
}

Pt_MedicalRecord {
	Txt k PK
	Num fk_ptID FK
	TS timeOfRecord "デフォルトはcreated_at"
	Txt medicalRecord "SOAP IOPなどテキストdata"
	Obj Obj1 "画像、スケッチなど"
	Obj Obj2
	Obj Obj3
	Obj Obj4
	Txt authorName
	Txt isActive "default True"
	Txt isApproved
	TS  created_at
	TS  modified_at
	TS  discarded_at
	Txt created_by
	Txt modified_by
	Txt discarded_by
	Txt approved_by "承認確認"
	TS approved_at
	Txt changeLog
}
Exm_VisualAcuity_Summary {
	Txt k PK
	Num fk_ptID FK
	TS timeOfRecord "デフォルトはcreated_at"
	Txt VASummaryTitle "眼鏡用など"
	Txt authorName
	Cal visualAcuitySummary
	Txt isTypicalValue "その日の代表検査値?"
	TS  created_at
	TS  modified_at
	TS  discarded_at
	Txt created_by
	Txt modified_by
	Txt discarded_by
	Txt isActive "default True"
}
Exm_VisualAcuity {
	Txt k PK
	Num fk_ptID FK
	Txt fk_Exm_VisualAcuity_Summary
	TS timeOfRecord "デフォルトはcreated_at"
	Txt VA_title "遠見、70cmなど"
	Txt eye "L, R, Bなど"
	Txt nakedVA
	Txt exm_condition "IOL, KB, CLなど"
	Txt correctedVA
	Txt	sphericalD
	Txt cylindricalD
	Txt axis
	Txt comment "+3Dから雲霧など"
	Txt isTypicalValue "その日の代表検査値"
	TS  created_at
	TS  modified_at
	Txt created_by
	Txt modified_by
}
Exm_IOP {
	Txt k PK
	Num fk_ptID FK
	TS timeOfRecord "デフォルトはcreated_at"
	Txt method "iCare, GAT, NCTなど"
	Num R_IOP[3]
	Num L_IOP[3]
	Cal R_avgIOP
	Cal L_avgIOP
	Num R_corrected_avgIOP
	Num L_corrected_avgIOP
	Txt comment
	TS  created_at
	TS  modified_at
	Txt created_by
	Txt modified_by
}
Pt_ObjRecord {
	Txt k PK
	Num fk_ptID FK
	TS timeOfRecord "デフォルトはcreated_at"
	Obj objData
	Obj thumbnail
	Txt thumbnail64
	Txt metaData
	Txt class "OCT, 紹介状, など"
	Txt tags
	Txt note
	
}
Pt_ReferralForm_Thread {
	Txt k PK "紹介状の一連のやり取りをthreadにまとめる"
	Num fk_ptID FK
	Data startDate "デフォルトはcreated_at"
	Date endDate "逆紹介日"
	Txt class "Send, Receive"
	Txt fk_ReferralHospital
	Txt refferalHospitalName "lookUp"
	Txt fk_RefferalDoctor
	Txt refferalDocotorName
	

}
Pt_ReferralForm_Archive {
	Txt k PK
	Num fk_ptID FK
	Txt fk_ReferralForm_Thread
	TS timeOfRecord "デフォルトはcreated_at"
	Txt class "Send, Receive"
	Txt type "診療情報提供書・報告書など"
	Txt fk_ReferralHospital
	Txt refferalHospitalName "lookUp"
	Txt fk_RefferalDoctor
	Txt refferalDocotorName
	Txt summary
	Txt note
	Obj Obj_1 "classにより異なるrelationの画像を登録"
	Obj Obj_2
	Obj Obj_3	
}
Pt_ReferralForm_Receive {
	Txt k PK
	Num fk_ptID FK
	Txt fk_ReferralForm_Thread "不要？"
	Txt fk_ReferralForm_Archive
	TS timeOfRecord "デフォルトはcreated_at"
	Txt class "Receive"
	Txt type "診療情報提供書・報告書など"
	Txt fk_ReferralHospital
	Txt refferalHospitalName "lookUp"
	Txt fk_RefferalDoctor
	Txt refferalDocotorName
	Txt summary
	Txt note
	Obj Obj_1
	Obj Obj_2
	Obj Obj_3
	Obj Obj_4
	Obj Obj_5
	Obj Obj_6
	Obj Obj_7
	Obj Obj_8
	Obj Obj_9
	Obj Obj_10
	Obj Obj_11
	Obj Obj_12

}
Pt_ReferralForm_Send {
	Txt k PK
	Num fk_ptID FK
	Txt fk_ReferralForm_Thread "不要？"
	Txt fk_ReferralForm_Archive
	TS timeOfRecord "デフォルトはcreated_at"
	Txt class "Send"
	Txt type "診療情報提供書・報告書など"
	Txt fk_ReferralHospital
	Txt refferalHospitalName "lookUp"
	Txt fk_RefferalDoctor
	Txt refferalDocotorName
	Txt summary
	Txt note
	Txt authorName
	Txt pt_name "look up"
	Txt pt_DOB "look up"
	Txt pt_sex "look up"
	Txt pt_address "look up"
	Txt pt_phoneNumber "look up"
	Txt byomei
	Txt purposeOfReferral
	Txt pastHistory
	Txt mainContent
	Txt drugPrescription
	Obj Obj_1
	Obj Obj_2
	Obj Obj_3
	Obj Obj_4　
	Obj Obj_5
	Obj Obj_6
	Obj Obj_7
	Obj Obj_8
	Obj Obj_9
	Obj Obj_10
	Obj Obj_11
	Obj Obj_12
	

}
Mst_ReferralHospital {
	Txt k PK

}
Mst_RefferalDoctor {
	Txt k PK
	
}
Mst_Byomei {

}
SYS_Notification {

	Txt k PK "ユーザーむけお知らせ機能"
	Txt sender_UserID
	Txt receiver_UserID
	Txt title
	Txt class "病名登録、採血結果、など？"
	Txt message
	Txt notification_json "ScriptName; ScriptParam; PtID "
	Txt isOpened_by_at "UserID|TS list"
	Txt isApproved_by
	TS  isApproved_at
	TS  created_at
	TS  modified_at
	Txt created_by
	Txt modified_by
}
HR_Account {
	Txt k PK
	Txt fk_Department FK
	Txt accountName
	Txt familyName
	Txt familyName_kana
	Txt firstName
	Txt firstName_kana
	Date dayOfBirth
	Txt sex
	Txt staffID
	Txt comment
	Date startDate
	Date endDate
	Txt isActive
	Txt changeLog
}
HR_Department {
	Txt k PK
	Txt name
	Txt code
	Txt explanation
}
HR_Role {
	Txt k PK
	Txt name
	Txt code
	Txt explanation
}
HR_Function {
	Txt k PK
	Txt name
	Txt code
	Txt explanation
}
HR_Role_Function {
	Txt k PK
	Txt fk_Role FK
	Txt fk_Function FK
}


```
