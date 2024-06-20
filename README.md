using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class BananaManController : MonoBehaviour
{
    public float moveSpeed = 3.0f;
    public float sprintSpeed = 5.0f;
    public float jumpForce = 5.0f;

    public Animator animator;
    private Rigidbody rb;
    private bool isGrounded = true;

    void Awake()
    {

        animator = GetComponent<Animator>();
        rb = GetComponent<Rigidbody>();
    }

    private void OnCollisionEnter(Collision collision)
    {
        if(collision.gameObject.CompareTag("Ground"))
        {
            isGrounded = true;
        }
    }
    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        // 키 입력을 받아 이동 및 회전 처리
        float horizontalInput = Input.GetAxis("Horizontal");
        float verticalInput = Input.GetAxis("Vertical");
        bool isSprinting = Input.GetKey(KeyCode.LeftShift) || Input.GetKey(KeyCode.RightShift);

        // 이동 방향 계산
        Vector3 moveDirection = new Vector3(horizontalInput, 0f, verticalInput).normalized;

        // 이동 및 회전 적용
        if (moveDirection != Vector3.zero)
        {
            // 이동 속도 설정
            float currentSpeed = isSprinting ? sprintSpeed : moveSpeed;
            Vector3 moveAmount = moveDirection * currentSpeed * Time.deltaTime;
            transform.Translate(moveAmount, Space.Self);

            animator.SetBool("isWalking", true);
            animator.SetBool("isRunning", isSprinting);
            animator.SetFloat("MoveX", moveDirection.x);
            animator.SetFloat("MoveZ", moveDirection.z);
        }
        else
        {
            // 이동 입력이 없을 때는 Idle 상태로 전환
            animator.SetBool("isWalking", false);
            animator.SetBool("isRunning", false);
            animator.SetFloat("MoveX", 0);
            animator.SetFloat("MoveZ", 0);
        }

        if(Input.GetKeyDown(KeyCode.Space) && isGrounded)
        {
            animator.SetTrigger("Jump");
            rb.AddForce(Vector3.up * jumpForce, ForceMode.Impulse);
            isGrounded = false;
        }
        

    }
}
