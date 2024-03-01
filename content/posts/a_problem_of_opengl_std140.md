---
title: "A_problem_of_opengl_std140"
date: 2024-02-29T14:27:23Z
draft: true
summary: "关于 OpenGL 的 std140 的一次内存布局问题"
tags: ["OpenGL"]
author: "IAKSH"
---

```glsl
layout (std140) uniform PhoneDirectLighting
{
    vec3 direction;
    vec3 ambient;
    vec3 diffuse;
    vec3 specular;
};

void main()
{
    fragColor = ambient * texture(skybox_cubemap,texCoord);
}
```

```cpp
struct PhoneDirectLightingData
{
	glm::vec3 direction;
	glm::vec3 ambient;
	glm::vec3 diffuse;
	glm::vec3 specular;
};
```

``` cpp
void quick3d::test::SkyboxRenderer::setup_phone_direct_lighting() noexcept
{
	phone_direct_lighting_ubo.dma_do([&](void* data)
	{
		auto ptr{ reinterpret_cast<PhoneDirectLightingData*>(data) };
		ptr->ambient = glm::vec3(0.5f, 0.5f, 0.5f);
		ptr->diffuse = glm::vec3(1.0f, 1.0f, 1.0f);
		ptr->specular = glm::vec3(0.5f, 0.5f, 0.5f);
		ptr->direction = glm::vec3(-0.2f, -1.0f, -0.3f);
	});
	glBindBufferBase(GL_UNIFORM_BUFFER, 2, phone_direct_lighting_ubo.get_buffer_id());
}
```

天空盒一直是蓝色的，在把diffuse从`(0.0f,0.0f,1.0f)`修改到`(1.0f, 1.0f, 1.0f)`之前甚至是黄色的。

怀疑是`std140`布局问题，其内存布局与C/C++ struct布局不一致。

> 位于std140布局的Uniform Block中的每个结构体按照它们在Uniform Block中出现的顺序存储。每个结构体及其成员对应有一个基偏移(base offset)和基对齐(base alignment)。对齐偏移由基偏移向上偏移满足偏移为基对齐的整数倍得到。结构体第一个成员的基偏移为结构体本身的基偏移，其它成员的基偏移由其上一个成员的对齐偏移加上上一个成员的机器大小再加1得到。Uniform Block的第一个成员基偏移为0。
	1: 如果成员是一个大小为N个机器单位的标量，它的基对齐为N。
	2: 如果成员是一个包含有2个元素或4个元素，每个元素大小为N个机器单位的向量，它的基对齐为2N或4N。
	3: 如果成员是一个包含有3个元素，每个元素大小为N个机器单位的向量，它的基对齐为4N。
	4: 如果成员是一个标量或向量数组，数组中的每个元素满足规则1，2，3，数组元素的基对齐向上扩充到vec4同样大小。数组尾部可能包含用于补齐的空白，数组之后的第一个成员的基偏移向上偏移到下一个基对齐的倍数。
	1:如果成员是一个列主序的C列R行矩阵，矩阵的存储方式和C个每个元素为包含R个元素的向量数组按照规则4存储相同。
	1: 如果成员是一个列主序的S个C列R行矩阵数组，矩阵的存储方式和S x C个每个元素为包含R个元素的向量数组按照规则4存储相同。
	1: 如果成员是一个行主序的C列R行矩阵，矩阵的存储方式和R个每个元素为包含C个元素的向量数组按照规则4存储相同。
	1: 如果成员是一个行主序的S个C列R行矩阵数组，矩阵的存储方式和S x R个每个元素为包含C个元素的向量数组按照规则4存储相同。
	1: 如果成员是一个结构体，它的基对齐和vec4相同。结构体内的成员递归按照规则1-10存储。结构体的第一个成员的偏移对齐等于结构体的偏移对齐。结构体的尾部可能包含有用于补齐的空白，紧接结构体的下一个成员的基偏移向上偏移到结构体的下一个基对齐。
	1: 如果成员是一个包含S个元素的结构体数组，这S个元素按照规则9依次存储
	[GLSL标准Uniform Block(std140)布局规则 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/568323076)

对于本次的问题而言，原因是上述的规则3。简单说就是`std140`会将`vec3`按照`vec4`进行内存布局。