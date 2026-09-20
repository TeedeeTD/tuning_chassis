# SO SÁNH CHI TIẾT THAM SỐ GIAO DIỆN PARAM ARDUROVER
**So sánh giữa `09082026_latest.param` và `param_tuning_1909_cuoiNgay.param`**

Tổng số tham số có sự sai khác: **192 tham số**.

### 1. Điều khiển Động lực học & Tune Xe (Motion Control & PID) (13 tham số)
| Tham số | `09082026_latest` | `1909_cuoiNgay` |
|---|---|---|
| `ACRO_TURN_RATE` | `50` | `30` |
| `ATC_ACCEL_MAX` | `1` | `0` |
| `ATC_BRAKE` | `1` | `0` |
| `ATC_DECEL_MAX` | `5` | `0` |
| `ATC_SPEED_I` | `0.2` | `0` |
| `ATC_SPEED_P` | `0.02` | `0.01` |
| `ATC_STR_ANG_P` | `2` | `3` |
| `ATC_STR_RAT_MAX` | `50` | `30` |
| `ATC_STR_RAT_P` | `0.2` | `0.3` |
| `ATC_TURN_MAX_G` | `0.6` | `0.2` |
| `GCS_PID_MASK` | `1` | `2` |
| `TURN_RADIUS` | `1.7` | `3` |
| `WP_RADIUS` | `1.7` | `3` |

### 2. Cấu hình Cổng CAN Bus (CAN Protocol & Driver) (38 tham số)
| Tham số | `09082026_latest` | `1909_cuoiNgay` |
|---|---|---|
| `CAN_D1_DSRM_STOP` | `1` | `MISSING` |
| `CAN_D1_PROTOCOL` | `15` | `1` |
| `CAN_D1_REV_SP` | `0` | `MISSING` |
| `CAN_D1_REV_ST` | `1` | `MISSING` |
| `CAN_D1_STEER_MAX` | `30` | `MISSING` |
| `CAN_D1_TIMEOUT` | `200` | `MISSING` |
| `CAN_D1_UC_ESC_BM` | `MISSING` | `0` |
| `CAN_D1_UC_ESC_OF` | `MISSING` | `0` |
| `CAN_D1_UC_ESC_RV` | `MISSING` | `0` |
| `CAN_D1_UC_NODE` | `MISSING` | `10` |
| `CAN_D1_UC_NTF_RT` | `MISSING` | `20` |
| `CAN_D1_UC_OPTION` | `MISSING` | `0` |
| `CAN_D1_UC_POOL` | `MISSING` | `16384` |
| `CAN_D1_UC_RLY_RT` | `MISSING` | `0` |
| `CAN_D1_UC_SER_EN` | `MISSING` | `0` |
| `CAN_D1_UC_SRV_BM` | `MISSING` | `0` |
| `CAN_D1_UC_SRV_RT` | `MISSING` | `50` |
| `CAN_D1_VEL_MAX` | `3` | `MISSING` |
| `CAN_D2_DSRM_STOP` | `MISSING` | `0` |
| `CAN_D2_PROTOCOL` | `1` | `15` |
| `CAN_D2_REV_SP` | `MISSING` | `0` |
| `CAN_D2_REV_ST` | `MISSING` | `0` |
| `CAN_D2_STEER_MAX` | `MISSING` | `30` |
| `CAN_D2_TIMEOUT` | `MISSING` | `200` |
| `CAN_D2_UC_ESC_BM` | `0` | `MISSING` |
| `CAN_D2_UC_ESC_OF` | `0` | `MISSING` |
| `CAN_D2_UC_ESC_RV` | `0` | `MISSING` |
| `CAN_D2_UC_NODE` | `10` | `MISSING` |
| `CAN_D2_UC_NTF_RT` | `20` | `MISSING` |
| `CAN_D2_UC_OPTION` | `0` | `MISSING` |
| `CAN_D2_UC_POOL` | `16384` | `MISSING` |
| `CAN_D2_UC_RLY_RT` | `0` | `MISSING` |
| `CAN_D2_UC_SER_EN` | `0` | `MISSING` |
| `CAN_D2_UC_SRV_BM` | `0` | `MISSING` |
| `CAN_D2_UC_SRV_RT` | `50` | `MISSING` |
| `CAN_D2_VEL_MAX` | `MISSING` | `3` |
| `CAN_P1_BITRATE` | `500000` | `1e+06` |
| `CAN_P2_BITRATE` | `1e+06` | `500000` |

### 3. Tính năng Firmware Tùy biến BAF & Network (33 tham số)
| Tham số | `09082026_latest` | `1909_cuoiNgay` |
|---|---|---|
| `FS_BAF_ENABLE` | `MISSING` | `1` |
| `FS_BAF_ESC_ACT` | `MISSING` | `2` |
| `FS_BAF_LED_EMPTY` | `MISSING` | `2` |
| `FS_BAF_LED_FAULT` | `MISSING` | `2` |
| `FS_BAF_LED_MID` | `MISSING` | `0.5` |
| `FS_BAF_LIDAR_ESC` | `MISSING` | `30` |
| `FS_BAF_LIDAR_MIN` | `MISSING` | `1` |
| `FS_BAF_LNK_TO` | `MISSING` | `3` |
| `FS_BAF_LOG` | `MISSING` | `1` |
| `FS_BAF_NET_ESC` | `MISSING` | `10` |
| `FS_BAF_PUMP` | `MISSING` | `1` |
| `FS_BAF_PUMP_MAX` | `MISSING` | `900` |
| `FS_BAF_RESUME` | `MISSING` | `2` |
| `FS_BAF_RTK_ESC` | `MISSING` | `10` |
| `FS_BAF_TFM_EN` | `MISSING` | `0` |
| `FS_BAF_TFM_ESC` | `MISSING` | `3` |
| `FS_BAF_TFM_HGT` | `MISSING` | `150` |
| `FS_BAF_TFM_INV` | `MISSING` | `0` |
| `FS_BAF_TFM_JUMP` | `MISSING` | `0` |
| `FS_BAF_TFM_MARG` | `MISSING` | `0` |
| `FS_BAF_TFM_TILT` | `MISSING` | `45` |
| `FS_BAF_VID_TO` | `MISSING` | `0` |
| `FS_BAF_WATER_TO` | `MISSING` | `3` |
| `FS_RTK_ENABLE` | `MISSING` | `0` |
| `NET_P3_IP0` | `MISSING` | `10` |
| `NET_P3_IP1` | `MISSING` | `36` |
| `NET_P3_IP2` | `MISSING` | `36` |
| `NET_P3_IP3` | `MISSING` | `63` |
| `NET_P3_PORT` | `MISSING` | `12002` |
| `NET_P3_PROTOCOL` | `MISSING` | `2` |
| `NET_P3_TYPE` | `0` | `1` |
| `RXTEL_ENABLE` | `MISSING` | `1` |
| `RXTEL_RATE` | `MISSING` | `10` |

### 4. Cấu hình Cổng Serial & Cảm biến Cảnh báo Khoảng cách (Rangefinder & Relay) (40 tham số)
| Tham số | `09082026_latest` | `1909_cuoiNgay` |
|---|---|---|
| `RELAY4_DEFAULT` | `MISSING` | `0` |
| `RELAY4_FUNCTION` | `0` | `1` |
| `RELAY4_INVERTED` | `MISSING` | `0` |
| `RELAY4_PIN` | `MISSING` | `54` |
| `RNGFND1_ADDR` | `MISSING` | `0` |
| `RNGFND1_FUNCTION` | `MISSING` | `0` |
| `RNGFND1_GNDCLR` | `MISSING` | `0.1` |
| `RNGFND1_MAX` | `MISSING` | `7` |
| `RNGFND1_MIN` | `MISSING` | `0.2` |
| `RNGFND1_OFFSET` | `MISSING` | `0` |
| `RNGFND1_ORIENT` | `MISSING` | `0` |
| `RNGFND1_PIN` | `MISSING` | `-1` |
| `RNGFND1_POS_X` | `MISSING` | `0` |
| `RNGFND1_POS_Y` | `MISSING` | `0` |
| `RNGFND1_POS_Z` | `MISSING` | `0` |
| `RNGFND1_PWRRNG` | `MISSING` | `0` |
| `RNGFND1_RMETRIC` | `MISSING` | `1` |
| `RNGFND1_SCALING` | `MISSING` | `3` |
| `RNGFND1_STOP_PIN` | `MISSING` | `-1` |
| `RNGFND1_TYPE` | `0` | `20` |
| `RNGFND2_ADDR` | `MISSING` | `0` |
| `RNGFND2_FUNCTION` | `MISSING` | `0` |
| `RNGFND2_GNDCLR` | `MISSING` | `0.1` |
| `RNGFND2_MAX` | `MISSING` | `7` |
| `RNGFND2_MIN` | `MISSING` | `0.2` |
| `RNGFND2_OFFSET` | `MISSING` | `0` |
| `RNGFND2_ORIENT` | `MISSING` | `0` |
| `RNGFND2_PIN` | `MISSING` | `-1` |
| `RNGFND2_POS_X` | `MISSING` | `0` |
| `RNGFND2_POS_Y` | `MISSING` | `0` |
| `RNGFND2_POS_Z` | `MISSING` | `0` |
| `RNGFND2_PWRRNG` | `MISSING` | `0` |
| `RNGFND2_RMETRIC` | `MISSING` | `1` |
| `RNGFND2_SCALING` | `MISSING` | `3` |
| `RNGFND2_STOP_PIN` | `MISSING` | `-1` |
| `RNGFND2_TYPE` | `0` | `20` |
| `SERIAL4_BAUD` | `230` | `115` |
| `SERIAL4_PROTOCOL` | `5` | `9` |
| `SERIAL5_BAUD` | `57` | `115` |
| `SERIAL5_PROTOCOL` | `2` | `9` |

### 5. Tránh Vật Cản & Hệ Thống Ghi Log (3 tham số)
| Tham số | `09082026_latest` | `1909_cuoiNgay` |
|---|---|---|
| `AVOID_BACKZ_SPD` | `0.75` | `0` |
| `LOG_FILE_DSRMROT` | `0` | `1` |
| `PRX_FILT` | `0.25` | `2` |

### 6. Tần số Phát Dữ liệu MAVLink Stream Rate (14 tham số)
| Tham số | `09082026_latest` | `1909_cuoiNgay` |
|---|---|---|
| `MAV4_EXTRA1` | `1` | `4` |
| `MAV4_EXTRA2` | `1` | `4` |
| `MAV4_EXTRA3` | `1` | `2` |
| `MAV4_EXT_STAT` | `1` | `2` |
| `MAV4_POSITION` | `1` | `2` |
| `MAV4_RAW_SENS` | `1` | `2` |
| `MAV4_RC_CHAN` | `1` | `2` |
| `MAV5_EXTRA1` | `4` | `1` |
| `MAV5_EXTRA2` | `4` | `1` |
| `MAV5_EXTRA3` | `2` | `1` |
| `MAV5_EXT_STAT` | `2` | `1` |
| `MAV5_POSITION` | `2` | `1` |
| `MAV5_RAW_SENS` | `2` | `1` |
| `MAV5_RC_CHAN` | `2` | `1` |

### 7. Hiệu Chẩn La Bàn, IMU & Thống Kê Hệ Thống (51 tham số)
| Tham số | `09082026_latest` | `1909_cuoiNgay` |
|---|---|---|
| `BARO1_GND_PRESS` | `99649.4` | `101540` |
| `BARO2_GND_PRESS` | `99377.5` | `101270` |
| `COMPASS_DEC` | `0` | `-0.0333716` |
| `COMPASS_DEV_ID` | `97291` | `0` |
| `COMPASS_DEV_ID2` | `331777` | `97283` |
| `COMPASS_DEV_ID3` | `0` | `331777` |
| `COMPASS_DIA2_X` | `0.8889` | `1` |
| `COMPASS_DIA2_Y` | `0.941387` | `1` |
| `COMPASS_DIA2_Z` | `0.918655` | `1` |
| `COMPASS_DIA3_X` | `1` | `0.8889` |
| `COMPASS_DIA3_Y` | `1` | `0.941387` |
| `COMPASS_DIA3_Z` | `1` | `0.918655` |
| `COMPASS_EXTERN2` | `0` | `1` |
| `COMPASS_EXTERN3` | `1` | `0` |
| `COMPASS_ODI2_X` | `0.00266193` | `0` |
| `COMPASS_ODI2_Y` | `0.0385853` | `0` |
| `COMPASS_ODI2_Z` | `0.000135482` | `0` |
| `COMPASS_ODI3_X` | `0` | `0.00266193` |
| `COMPASS_ODI3_Y` | `0` | `0.0385853` |
| `COMPASS_ODI3_Z` | `0` | `0.000135482` |
| `COMPASS_OFS2_X` | `149.564` | `-131.025` |
| `COMPASS_OFS2_Y` | `26.0891` | `-141.639` |
| `COMPASS_OFS2_Z` | `75.3636` | `-72.3353` |
| `COMPASS_OFS3_X` | `-131.025` | `149.564` |
| `COMPASS_OFS3_Y` | `-141.639` | `26.0891` |
| `COMPASS_OFS3_Z` | `-72.3353` | `75.3636` |
| `COMPASS_ORIENT2` | `0` | `29` |
| `COMPASS_ORIENT3` | `29` | `0` |
| `COMPASS_PRIO2_ID` | `331777` | `97283` |
| `COMPASS_PRIO3_ID` | `0` | `331777` |
| `COMPASS_SCALE2` | `1.22814` | `1.07778` |
| `COMPASS_SCALE3` | `1.07778` | `1.22814` |
| `COMPASS_USE` | `1` | `0` |
| `COMPASS_USE2` | `0` | `1` |
| `INS_GYR1_CALTEMP` | `27.0625` | `45.6562` |
| `INS_GYR2OFFS_X` | `0.000553221` | `-0.000403517` |
| `INS_GYR2OFFS_Y` | `0.00261909` | `0.00251216` |
| `INS_GYR2OFFS_Z` | `1.63161e-05` | `0.00117001` |
| `INS_GYR2_CALTEMP` | `27.25` | `45.5938` |
| `INS_GYR3OFFS_X` | `0.00282186` | `0.0033356` |
| `INS_GYR3OFFS_Y` | `0.00589214` | `0.00615716` |
| `INS_GYR3_CALTEMP` | `27` | `45.7188` |
| `INS_GYROFFS_X` | `0.00417658` | `0.0028718` |
| `INS_GYROFFS_Y` | `-0.00243648` | `-1.07877e-05` |
| `INS_GYROFFS_Z` | `0.00131601` | `0.00147171` |
| `MIS_TOTAL` | `12` | `4` |
| `STAT_BOOTCNT` | `668` | `887` |
| `STAT_DISTFLWN` | `44971.2` | `53820` |
| `STAT_FLTCNT` | `3721` | `4476` |
| `STAT_FLTTIME` | `43541` | `54280` |
| `STAT_RUNTIME` | `3.4248e+06` | `3.9316e+06` |
