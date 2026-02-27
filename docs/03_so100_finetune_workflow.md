# SO100 Fine-tuning Workflow (Teleop → Record → Upload → Train → Deploy)

This doc describes the practical workflow for collecting data and fine-tuning policies on **LeRobot SO100**:
- Teleoperate (leader → follower)
- Record dataset locally
- (Optional) Upload/merge datasets on HuggingFace
- Train policies (SmolVLA / ACT / π0) on AutoDL
- Deploy & evaluate on the robot

---

## 0) Device mapping (IMPORTANT)

### Robot USB ports
In our setup:
- `/dev/ttyACM0` → **so100_follower** (robot)
- `/dev/ttyACM1` → **so100_leader** (teleop)

> Your mapping may differ. Verify with:
```bash
ls -l /dev/ttyACM*
```

### Camera device indices (IMPORTANT)
Our cameras:
top camera → /dev/video0
handeye camera → /dev/video4

Verify with:
```bash
ls -l /dev/video*
v4l2-ctl --list-devices
```
Tip: UVC cameras usually appear in pairs (0/1, 2/3, 4/5). Use the even index for the actual video stream.
