---
title: "Opengl_cutting_distance_cant_be_crazy"
date: 2024-02-29T14:29:25Z
draft: true
summary: "OpenGL的相机距离裁切不能太小或太大"
---

FPSCamera(int screen_width, int screen_height, glm::vec3 start_pos = glm::vec3(0.0f, 0.0f, 0.0f), float start_yaw = -90.0f,
    float start_pitch = 0.0f, float speed = 0.000001f, float sensitivity = 0.1f, float start_fov = 45.0f, float start_near = 0.00001f,
    float start_far = 1000000.0f) noexcept
```
比如这样的值，就会出问题，光影乱蹦。